
# reload-server
> Runs commands when files change then sends an event to the client over SSE.

## TODO
+ Inject reload script into the client server side instead of importing it
+ ESBuild outputs empty app.js files when there's a build error so the client won't auto reconnect (non-issue?)
+ Refine the file extension flag syntax. ex: `--ext:js <command> --ext:css <command>`
+ Use http2?

## Usage

Example using a makefile.
The `--bang` flag runs the following command immediately after the server starts.
Flags like `--js` and `-scss` are flags that correlate to file extensions.
These extension flags can be anything.

```makefile
reload-server \
  --bang "$(MAKE) css js html" \
  --scss "$(MAKE) css" \
  --js "$(MAKE) js" \
  --watch "src"
```

Include this in your JavaScript.

```
if (DEV === true) {
  const source = new EventSource('/reload')

  source.onmessage = body => {
    if (body.data === 'connect') {
      console.log('Connected to automatic reload')
      return // stop execution
    }

    if (body.data === 'reload') {
      window.location.reload()
      return // stop execution
    }

    if (body.data === undefined) {
      console.log('Heartbeat from automatic reload')
    }
  }
}
```
