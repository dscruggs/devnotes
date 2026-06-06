# File Storage and CDNs

## File storage

- `large files` are giant blobs of data encoded in specific file formats
- this can lead to performance bottlenecks when accessed frequently, as opposed to DB records which are typically smaller (but you can have files in dbs too)
- usually files contain their signature set of bytes in the first line, can use `xxd` to view file bytestream. Something like `xxd -l 8 <file>`

### Multipart uploads

- for large data and files in web forms you can use something called [multipart/form-data](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/POST#multipart_form_submission) which sends multiple pieces of data in a single request

### Encoding in DBs (don't do this)

- you can do encoding like base64 string and just upload files to a string column in a database, but this has problems
    - CPU hog
    - high storage
    - database is unoptimized here
    - caching doesn't really work well

### Using the filesystem

- filesystems are optimized for storing and serving files. So a good pattern is to store the filepath to the image in the DB rather than the content itself

### MIME types

- these are just web-friendly ways of describing the format of a file. More standardized than normal file extensions. look like:
    - image/png
    - video/mp4
    - audio/mp3
    - text/html

---

## Caching

- `cache` is just a temporary, faster form of storage that makes sure you don't need to make a request to a server
- `cache busting` means to force retrieval of the latest version of something. This is done by making the browser think it's a different file but the server thinks it's the same file.
    - this can be done by something like a query parameter because oftentimes servers ignore these if they're not needed. So you can pass the same image url but w/ a query parameter to change the path which busts the cache. This is something you can do if you don't have any control over the server and there's not a preferred method
    - the preferred way is to use `Cache-Control` header in the request

---

## AWS S3

- `serverless` is just an architecture that means you don't need to manage servers on your own
- Instead of a local file system you just make API requests to read and write files to S3
- S3 objects just need 2 components to access: the bucket name and the object key

## Object storage

- `file storage` is storing files in a hierarchy or directories w/ system-level metadata like timestamp and permissions managed by the filesystem, not the file itself
- `object storage` is different
    - objects are stored in a flat namespace (no directories, but you can still use filepaths to symbolize that)
    - an object's metadata is stored with the object itself
- so object stores can kind of feel like file systems because of the illusion of directories by allowing slashes in their namespace

---

## Video Streaming

- `downloading` is when you wait for the entire file to be transferred before you can start using it
- `streaming` is when you start using the file immediately while it's still being transferred in the background
- how to stream videos?
    - the native HTML5 video element streams video files by default as long as the server supports it
    - the `range` http header allows the client to request specific byte ranges of a file enabling partial downloads. S3 supports this by default
    - a `206` status code means partial content and is a success
- for streaming files sometimes you need to fetch the end of the file first to get important metadata to be able to render, you can't just go in order. like in videos there's something called a `moov` atom
- HLS or MPEG-DASH formats allows for changing the quality of the stream on the fly
- WEBRTC or RTMP can be used for low latency live streaming

---

## Security

- one layer of cloud security is to make sure your keys are secure
- another is to make sure you can only access cloud resources from specific locations
- `principle of least privileges`, your app/creds should only be able to do what it needs to do
- roles assign specific permissions
- use https so your data is encrypted in transit

---

## Content Delivery Network (CDN)

- a network of servers that caches and delivers content to users based on their geographic location
- this helps with the reality of trying to get data from ohio in australia by locally caching files
- cloudfront on AWS helps with this

---

## Resiliency

- `availability` is how often your service is up and running
- `reliability` is how well your system works when its up. So a service could respond with an http request but the data is wrong (that's not reliable)
- `durability` is how well your data survives in the event of an outage
- `consistency` is kindof like reliability but this is just making sure the data returned/performance is the same whether or not a system is under load and when two similar requests come they get the same result
- there's a tradeoff on all of these in how you implement systems
