# CommandBox Docker Rewrite Behavior for Existing Folders

CommandBox (due to Runwar?) returns a 302 response when an existing directory is accessed without a trailing slash. Non-existing paths are handled differently.

## Steps to reproduce with this repository

1. `docker-compose up`
2. `curl -I http://127.0.0.1:8080/views`  
    Response:

    ```text
    HTTP/1.1 302 Found
    Connection: keep-alive
    Location: http://127.0.0.1:8080/views/
    ```

    The logs appear like this:

    ```text
    [ERROR] runwar.context: Location: '/views' generated no content, maybe verify any errorPage locations? (status code: 302)
    ```

    The 302 response is unexpected. Additionally, `errorPages` are defined in `server.json`.

3. If the url is curled with the trailing slash, the expected `403` is returned: `curl -I http://127.0.0.1:8080/views/`  
   Response:

   ```text
   HTTP/1.1 403 Forbidden
   ```

4. For non-existent paths, a 404 is returned, as expected: `curl -I http://127.0.0.1:8080/fake`  
    Response:

    ```text
    HTTP/1.1 404 Not Found
    ```

5. While enabling rewrites changes how non-existent paths are handled (they route to index and return 200), existing folders still return a 302.

## Live example to reproduce

You can see this behavior with the live ForgeBox site:

```text
curl -I https://forgebox.io/modules

HTTP/2 302
date: Tue, 14 May 2019 19:48:37 GMT
content-length: 0
location: https://forgebox.io/modules/
```

The path, with the trailing slash, returns a 403 as expected.