# insnapshot

A bash script that watches a directory and automatically creates a snapshot of
it when the content changes


## Warning

This script is in very early stages of development.


## Usage

```
insnapshot [OPTION...] [SRC] [DEST]

Watches the SRC directory for changes and creates snapshots under the DEST
directory. If SRC is omitted, the current working directory is used. If DEST
is omitted, it will be set to /old-versions.

Options:

  --exclude-from=FILE        File specifying exclude patterns passed to rsync,
                             see there [default: /home/schooft/.rsync/exclude]
  --wait=SECONDS             Wait until there are no changes for the specified
                             number of seconds [default: 2]
  --max-wait=SECONDS         When there are ongoing consecutive, rapid changes,
                             wait at most this number of seconds [default: 300]
  --min-interval=SECONDS     Minimum duration between two snapshots in seconds
                             [default: 900]
  --max-size=SIZE            Don't baclup any file larger than SIZE, see rsync
                             [default: ]
  --oneshot                  Make a snapshot immediately and exit
  -h, --help                 Print help and exit
  --version                  Print version and exit
```


## How does it work

`insnapshot` utilizes `inotifywatch` to be efficiently notified on any changes
in the watched directory. It then waits until there are no changes for a few
seconds to combine rapid consecutive changes (but it waits at maximum a few
minutes) and then calls `rsync` to create a snapshot in the target directory
und the currect date and time. It then sleeps for some time to keep the number
of snapshots reasonable.

The `--link-dest` option of `rsync` allows for space efficient storage as
unchanged files are simply hard linked to the previous snapshot. Nevertheless,
each snapshot appears as a full copy of the source directory, allowing for easy
recovery of old versions.


## Requirements

* `inotifywait`
* `rsync`

In addition, the file system of the target directory must support hard links.


## Limitations

* A snapshot state can be inconsistent if changes happen during a backup


## Contributing

Please feel free to open issues or pull requests.


## Acknowledgments

This script was inspired by [gitwatch](https://github.com/gitwatch/gitwatch)
and [rsync time
machine](http://blog.interlinked.org/tutorials/rsync_time_machine.html).
