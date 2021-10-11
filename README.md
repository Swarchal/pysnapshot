# PySnapshot

Simple filesystem monitoring method without relying on inotify or mtime.
Basically a set using sqlite.

Records the names of directories in a parent directory in a database
alongside a hash, creating a snapshot. This can then be used to determine
if new files or directories have been exported since the last time it has
been run. It is expected that this is ran as a cronjob.

There is also the option to filter the files/directories based on
a regex string.

An example workflow:

```python
import sys
from snapshot import Snapshot


results_dir = "/mnt/proj-c19/ABNEUTRALISATION/NA_raw_data"
regex_filter = regex=r"^[S|T].*/*Measurement 1$"

snapshot = Snapshot(results_dir, regex=regex_filter)

if snapshot.current_hash == snapshot.stored_hash:
    # nothing has changed since last time
    sys.exit(0)

# get new directory names
new_data = snapshot.get_new_dirs()

# record new snapshot
snapshot.make_snapshot()

if new_data:
    # do stuff with new data
```
