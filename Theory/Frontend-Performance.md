# Roadmap.sh

## Frontend Performance

### Low priority

Pre-load urls where possible, concatenate css into a single file, remove unused css, use woff2 font format, use preconnect to load our font faster, keep the web font size nder 300kb, prevent flash or invisible text, keep an eye on the size of dependencies

### Medium priority

Minified html-remove comments and witespaces, use content delivery network, prefer using vector image rather than bitmap images, set width and heiht atributes on images aspect ratio, avoid using base64 images, offscreen images are loaded lazily, ensure to serve images that are closed to your display size, avoid multiple inline javascript snippets <script>, keep your dependencies up to date, check for performance problems in your javascript files, service workers for caching/performing heavy tasks, cookie size, should be less than 4096 bytes, keep the cookie count less than 20

### Hihgt priority

Keep your page weight < 1500kb idealy < 500kb, keep our page load time < 3 seconds, gzip/brolit compression is enabled, minimize http requests, compress our images/keep the image count low, set http cache headers properly, keep the time to first byte < 1.3 seconds, non blocking javascript use async/defer, minify your javascript, minified css, remove comments, withespaces etc, inline the critical css above the fold css, cssfiles are non blocking, use https on your website, choose your image format appropiotely, avoid requesting unreachable files 404, serve files from the same protocol, sminizmize numbers of iframes, avoid the embedded inline css, analyse stylessheets complexity.

### Performance tools

page speed insights, lighthouse, webpage test, chrome devtools, bundlephobia, squoosh.app