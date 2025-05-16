# How to use in Caddy

This setup allows you to serve **custom error pages** in your Caddy server configuration using reusable **snippets** for both:

- **Upstream response errors** (e.g. Not found errors)
- **General proxy/server errors** (e.g. Caddy-level issues)

## 📁 Error Pages
Download the ZIP from [README.md](./README.md) and extract all html files into /errors (or anyo ther directory)

## 📦 Snippets
Add the following two snippets at the top of your `Caddyfile`:

```caddyfile
(upstream_error_handler) {
    @error status 400 401 402 403 404 405 406 407 408 409 410 411 412 413 414 415 416 417 418 419 420 421 422 423 424 425 426 427 428 429 430 500 501 502 503 504 505 506 507 508 509 510 511
    handle_response @error {
        root * /errors
        rewrite * /http-{rp.status_code}.html
        templates
        file_server
    }
}

(proxy_error_handler) {
    handle_errors {
        root * /errors
        rewrite * /http-{err.status_code}.html
        templates
        file_server
    }
}
```

⚠️ Using **`templates`** can leak information such as internal URLs or IPs! USE WITH CAUTION!  
⚠️ Depending on your error directory, you might have to change the `root` directive in the Snippets!

## 🛠 How to Use
Add the following imports into your `handler` or `reverse_proxy` blocks

```caddyfile
example.com {
    import proxy_error_handler # Handles errors caused by the proxy (e.g. includes 501)

    reverse_proxy http://localhost:8080 {
        import upstream_error_handler # Handles errors caused / returned from upstream
    }
}
```