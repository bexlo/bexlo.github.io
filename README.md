# ibexlo.github.io
ibexlo.com homepage

## Security hardening notes

This site is served from GitHub Pages (`Server: GitHub.com` on https://www.bexlo.co.kr).

### SRI hashes applied

The following assets are now referenced with `integrity` (`sha384`) and `crossorigin="anonymous"` in all HTML pages:

- `css/font-icons.min.css`: `sha384-WYF9R//8TDNSmRPhtLUDC3W+869FIi4IpkNdpbHXux1ODxh4sozA7pqI3338XFIg`
- `css/theme-vendors.min.css`: `sha384-Dk7vK8PD6lQLnW2WEjsFaNxqT2q87oPJgZh+LR9YzYPhhcQ3pEJHF0fctADBbT0J`
- `css/style.css`: `sha384-jdhR5S/AIfzTeRGzHOBJE48PGO3O54VVg+Gj1zGFQjRBWtqfXONXlMmbf+0nGW0w`
- `css/responsive.css`: `sha384-Bpagb/rX6cwC/ewh/nJ6LSxDCvX8JRSLn5YxH0VdkryEG4TNsVdFue/N6Sr7Qqp9`
- `css/ibex.css`: `sha384-ckDlYw2FzywCCw+QElvPi5+MEVBzAq7/P/rKK0DaTtgFdwvmOzK9LH0K7PWwK+23`
- `js/jquery.min.js`: `sha384-kMhb2a62f93cRiCw1AxAD+mrzY1Cnqes7A2q76CO7RELATYAnimF4nUBzsGOuKf9`
- `js/theme-vendors.min.js`: `sha384-z8LXTSwwZMaGsfupfuJ9Y5fCrhAsaPzsSfLoWk1n+9M2Jm2tacExLF4/C62CS0Nh`
- `js/main.js`: `sha384-XrEW719+CpMjMMOGvp5ToTnSZe/nJv1v641qhmyPKci+rRq3uPjYexmd7Jk7AtRk`

### CSP

All HTML pages now include a CSP meta tag in `<head>`.

### X-Content-Type-Options (`nosniff`)

GitHub Pages does not support custom response headers per-repository.
`<meta http-equiv="X-Content-Type-Options" ...>` is added for visibility, but browser enforcement is based on HTTP response headers.

To fully satisfy scanner checks for `X-Content-Type-Options: nosniff` and header-based CSP, place a proxy/CDN in front of GitHub Pages (for example Cloudflare, Nginx, or Azure Front Door) and inject these headers at the edge.
