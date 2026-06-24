# ibexlo.github.io
ibexlo.com homepage

## Security hardening notes

This site is served from GitHub Pages (`Server: GitHub.com` on https://www.bexlo.co.kr).

### SRI hashes applied

The following assets are now referenced with `integrity` (`sha384`) and `crossorigin="anonymous"` in all HTML pages:

- `css/font-icons.min.css`: `sha384-WYF9R//8TDNSmRPhtLUDC3W+869FIi4IpkNdpbHXux1ODxh4sozA7pqI3338XFIg`
- `css/theme-vendors.min.css`: `sha384-Dk7vK8PD6lQLnW2WEjsFaNxqT2q87oPJgZh+LR9YzYPhhcQ3pEJHF0fctADBbT0J`
- `css/style.css`: `sha384-GqNi0AWtltMpY1dIAW9My1N+eM70acy/QyaJUKCULuIkgrNpi2Y5mQHzjWW8neoG`
- `css/responsive.css`: `sha384-Bpagb/rX6cwC/ewh/nJ6LSxDCvX8JRSLn5YxH0VdkryEG4TNsVdFue/N6Sr7Qqp9`
- `css/ibex.css`: `sha384-U7OH5zcQbL7S/BhqWkpL6/1Qmj989e03HGWsmsyQwPKTMFoYuKjStaY9eqogu9SG`
- `js/jquery.min.js`: `sha384-1D5yKv88o8J0QouShbmMBe6qT++1JwqOQIiRnBRPy1daYGjvidTPhG4nHWnzKada`
- `js/theme-vendors.min.js`: `sha384-uLvVdu/Y5oNth34ZdaoQi6QXc7j8q5+KXxJF0ExharLxnZ9TCbgqJo8ZGExywovw`
- `js/main.js`: `sha384-XrEW719+CpMjMMOGvp5ToTnSZe/nJv1v641qhmyPKci+rRq3uPjYexmd7Jk7AtRk`

### CSP

All HTML pages now include a CSP meta tag in `<head>`.

### X-Content-Type-Options (`nosniff`)

GitHub Pages does not support custom response headers per-repository.
`<meta http-equiv="X-Content-Type-Options" ...>` is added for visibility, but browser enforcement is based on HTTP response headers.

To fully satisfy scanner checks for `X-Content-Type-Options: nosniff` and header-based CSP, place a proxy/CDN in front of GitHub Pages (for example Cloudflare, Nginx, or Azure Front Door) and inject these headers at the edge.

### Scanner items that cannot be fixed by repository files alone

GitHub Pages does not allow per-site custom response headers. This means the following findings need edge configuration (CDN/reverse proxy), not just HTML changes:

- `no_hsts`
- `x_content_type_options_missing`
- `no_csp_header_x_frame_options_missing`
- redirect pattern controls (`https_redirect_add_subdomain`)

Recommended edge headers:

- `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: SAMEORIGIN` (or `DENY` if embedding is unnecessary)
- `Content-Security-Policy: default-src 'self'; script-src 'self'; style-src 'self' https://fonts.googleapis.com; img-src 'self' data: https:; font-src 'self' https://fonts.gstatic.com data:; connect-src 'self'; frame-src https://www.google.com https://maps.google.com; frame-ancestors 'self'; object-src 'none'; base-uri 'self'; form-action 'self'; upgrade-insecure-requests`

Recommended redirect rule:

- HTTP requests should first be upgraded on the same host (`http://bexlo.co.kr` -> `https://bexlo.co.kr`), then canonical host redirect can happen over HTTPS if needed.
