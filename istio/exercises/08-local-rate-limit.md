# Rate Limiting 

  * Configuration is not done **on** the HTTPRoute/Gateway objects themselves.
  * Done via an `EnvoyFilter` that targets the Istio ingress gateway.

```

```

Below is a concrete minimal example for **local rate limiting at the Istio IngressGateway**, which works regardless of whether you use `VirtualService` or `HTTPRoute`.

---

## 1. Assumptions

* You installed Istio with the default ingress gateway in `istio-system`.
* The ingress pod has the usual label `istio: ingressgateway` (check with `kubectl get pod -n istio-system --show-labels`).
* You’re already using **Gateway API** (`Gateway` + `HTTPRoute`) to expose your app.

---

## 2. Local rate limit on the ingress gateway (all routes)

This example limits **all traffic through the ingress gateway** to **10 requests per 60 seconds per gateway pod** (local rate limit).

Apply this in `istio-system`:

kubectl apply -n bookinfo -f - <<'EOF'
apiVersion: networking.istio.io/v1alpha3
kind: EnvoyFilter
metadata:
  name: bookinfo-gateway-local-ratelimit-per-minute
spec:
  workloadSelector:
    labels:
      gateway.networking.k8s.io/gateway-name: bookinfo-gateway
  configPatches:
    - applyTo: HTTP_FILTER
      match:
        context: GATEWAY
        listener:
          filterChain:
            filter:
              name: "envoy.filters.network.http_connection_manager"
      patch:
        operation: INSERT_BEFORE
        value:
          name: envoy.filters.http.local_ratelimit
          typed_config:
            "@type": type.googleapis.com/udpa.type.v1.TypedStruct
            type_url: type.googleapis.com/envoy.extensions.filters.http.local_ratelimit.v3.LocalRateLimit
            value:
              stat_prefix: bookinfo_gateway_local_ratelimiter
              token_bucket:
                max_tokens: 10        # maximal im Bucket
                tokens_per_fill: 10   # 10 neue Tokens
                fill_interval: 60s    # jede Minute
              filter_enabled:
                runtime_key: local_rate_limit_enabled
                default_value:
                  numerator: 100
                  denominator: HUNDRED
              filter_enforced:
                runtime_key: local_rate_limit_enforced
                default_value:
                  numerator: 100
                  denominator: HUNDRED
              response_headers_to_add:
                - append: false
                  header:
                    key: x-local-rate-limit
                    value: "true"
```

What this does (in short):

* Inserts the Envoy `local_ratelimit` HTTP filter into the ingress gateway’s HTTP filter chain. ([Istio][1])
* Uses a token-bucket algorithm: 10 requests allowed per 60s per **gateway pod**. Extra requests get `429 Too Many Requests` with header `x-local-rate-limit: true`.
* Applies at the **gateway** level (`context: GATEWAY`), so it affects all your `HTTPRoute`s automatically.

> ⚠️ If you run multiple ingress gateway replicas, the limit is per-pod.
> E.g. 3 replicas × 10 req/60s ≈ 30 req/60s total.

---

## 3. How to test it

After applying the `EnvoyFilter`:

```bash
# Replace with your real host
HOST="your.domain.example"

for i in $(seq 1 20); do
  code=$(curl -s -o /dev/null -w "%{http_code}" "http://$HOST/")
  echo "Request $i -> HTTP $code"
done
```

Expected:

* First ~10 requests: `200`
* After that: `429` and `x-local-rate-limit: true` in the response headers.

---

## 4. Typical gotchas

1. **Wrong label / no `workloadSelector`**
   Then the filter either never applies or applies to every proxy in the namespace. Always check:

   ```bash
   kubectl get pod -n istio-system --show-labels | grep ingress
   ```

2. **Wrong `context`**

   * `SIDECAR_INBOUND`: for in-mesh services
   * `GATEWAY`: for ingress/egress gateways
     For Gateway API ingress you want `context: GATEWAY`.

3. **Namespace mismatch**
   `EnvoyFilter` must live in the **same namespace** as the workload you select (here: `istio-system`). ([imesh.ai][2])

---

## 5. And if you want more advanced (global) rate limiting…

* **Local rate limit** (above) is simple and doesn’t require extra services, but the limit is **per pod**.
* **Global rate limit** uses a separate rate-limit service + Redis and an Envoy HTTP rate limit filter, configured via another `EnvoyFilter`. ([Istio][1])

If you actually run **Envoy Gateway** (separate project) as your ingress, there are Gateway-API-style CRDs (e.g. policies) for rate limiting instead of raw `EnvoyFilter`. ([Huabing Blog][3])

---

If you tell me:

* whether you want **per-client-IP** limits or just a plain “N requests per second”, and
* which host/path you want to protect,

I can adapt this to a more specific example (e.g. only for `GET /login` on one `HTTPRoute`).

[1]: https://istio.io/latest/docs/tasks/policy-enforcement/rate-limit/?utm_source=chatgpt.com "Enabling Rate Limits using Envoy"
[2]: https://imesh.ai/blog/istio-rate-limiting-local/?utm_source=chatgpt.com "How to Configure Istio Local Rate Limiting - IMESH"
[3]: https://www.zhaohuabing.com/post/2025-07-23-use-envoy-gateway-as-the-ingress-gateway-and-waypoint-proxy-for-ambient-mesh/?utm_source=chatgpt.com "Bringing Full L7 Power to Istio Ambient Mesh with Envoy ..."
