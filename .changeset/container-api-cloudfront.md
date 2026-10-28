---
'@aws-blocks/core': minor
'@aws-blocks/blocks': patch
'@aws-blocks/bb-compute': patch
'@aws-blocks/bb-container-compute': patch
---

Under `apiFrontDoor: 'cloudfront'`, and behind `Hosting`'s CloudFront door on either tier, CloudFront now routes an API namespace on a `container` compute straight to the container's load balancer, instead of through the shared API gateway. A call can run up to 60 seconds (CloudFront's origin read timeout) instead of the gateway's ~29, and skips a hop.

- Each container namespace path (`/aws-blocks/api/<export name>`) gets its own CloudFront behavior, ahead of the `/aws-blocks/api/*` gateway behavior, all on one VPC origin for the backend's load balancer listener (whose path rules pick the compute) — so a backend uses one of the account's VPC origins (25 by default) however many containers serve API traffic. Namespaces on Lambda computes, auth routes and `RawRoute`s still go through the gateway.
- A viewer-request CloudFront Function stamps the viewer's host on those requests, so `ctx.request.url` in the container is the public URL, including a custom domain.
- The load balancer admits CloudFront's origin-facing prefix list on its listener ports; the prefix list is looked up at deploy time, so stacks stay environment-agnostic.
- Without a CloudFront front door (the `apigateway` tier without `Hosting`, or `Hosting`'s ALB, API Gateway, stacked or custom doors), calls still go through the gateway.
- With `apiWaf` on the `apigateway` tier, the stage's web ACL is now also associated with the container load balancer that `Hosting`'s CloudFront reaches directly. Those requests and the gateway's VPC link requests carry the front-door marker, so a Blocks-managed web ACL's rate rule exempts them; a `webAclArn` of your own is associated as-is, with a synth warning.
