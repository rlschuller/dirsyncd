# dirsyncd

A simple on-demand file sync daemon that employs a server/client strategy to
avoid copying/moving incomplete files or directories.


## How it works

**dirsync** reads 3 core environment variables:

1. `DIRSYNCD_ORIG_DIR`

    The origin directory

2. `DIRSYNCD_DEST_DIR`

    The destination directory

3. `DIRSYNCD_REQ_DIR`

    A directory with all the sync requests

Whenever a file whose name starts with *request* is found in `DIRSYNC_REQ_DIR`,
it's first line is interpreted as a relative path in `DIRSYNC_ORIG_DIR`. This
path is then synced to `DIRSYNC_DEST_DIR` with mirrored file structure. Finally,
the request file is erased and the procedure is repeated.


## Optional Parameters

1. `DIRSYNC_SLEEP_TIME`

    Sleep time (in sec) between loop iterations - default is 600.

2. `DIRSYNC_REMOVE_SOURCE_FILES`

    If set to **true**, remove files from `DIRSYNC_ORIG_DIR`.


## Observations

For POSIX complient filesystems, move is an atomic operation, i.e., thread
safe. It's a good idea to write the path to a temporary filename in
`DIRSYNC_REQ_DIR` and then rename it to *request_<random_string>*.

A lock file is created in `DIRSYNC_REQ_DIR` to avoid running more than one
server. To run multiple servers, the clients must write one request per server.
