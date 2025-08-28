# buildx du

```text
docker buildx du [OPTIONS]
```

<!---MARKER_GEN_START-->
Disk usage

### Options

| Name                    | Type     | Default | Description                              |
|:------------------------|:---------|:--------|:-----------------------------------------|
| [`--builder`](#builder) | `string` |         | Override the configured builder instance |
| `-D`, `--debug`         | `bool`   |         | Enable debug logging                     |
| [`--filter`](#filter)   | `filter` |         | Provide filter values                    |
| [`--format`](#format)   | `string` |         | Format the output                        |
| [`--verbose`](#verbose) | `bool`   |         | Shorthand for `--format=pretty`          |


<!---MARKER_GEN_END-->

## Examples

### Show disk usage

The `docker buildx du` command shows the disk usage for the currently selected
builder.

```console
$ docker buildx du
ID                                RECLAIMABLE    SIZE          LAST ACCESSED
12wgll9os87pazzft8lt0yztp*        true           1.704GB       13 days ago
iupsv3it5ubh92aweb7c1wojc*        true           1.297GB       36 minutes ago
ek4ve8h4obyv5kld6vicmtqyn         true           811.7MB       13 days ago
isovrfnmkelzhtdx942w9vjcb*        true           811.7MB       13 days ago
0jty7mjrndi1yo7xkv1baralh         true           810.5MB       13 days ago
jyzkefmsysqiaakgwmjgxjpcz*        true           810.5MB       13 days ago
z8w1y95jn93gvj92jtaj6uhwk         true           318MB         2 weeks ago
rz2zgfcwlfxsxd7d41w2sz2tt         true           8.224kB*      43 hours ago
n5bkzpewmk2eiu6hn9tzx18jd         true           8.224kB*      43 hours ago
ao94g6vtbzdl6k5zgdmrmnwpt         true           8.224kB*      43 hours ago
2pyjep7njm0wh39vcingxb97i         true           8.224kB*      43 hours ago
Shared:        115.5MB
Private:       10.25GB
Reclaimable:   10.36GB
Total:         10.36GB
```

If `RECLAIMABLE` is false, the `docker buildx du prune` command won't delete
the record, even if you use `--all`. That's because the record is actively in
use by some component of the builder.

The asterisks (\*) in the default output format indicate the following:

- An asterisk next to an ID (`zu7m6evdpebh5h8kfkpw9dlf2*`) indicates that the record
  is mutable. The size of the record may change, or another build can take ownership of
  it and change or commit to it. If you run the `du` command again, this item may
  not be there anymore, or the size might be different.
- An asterisk next to a size (`8.288kB*`) indicates that the record is shared.
  Storage of the record is shared with some other resource, typically an image.
  If you prune such a record then you will lose build cache but only metadata
  will be deleted as the image still needs to actual storage layers.

### <a name="filter"></a> Provide filter values (--filter)

Same as [`buildx prune --filter`](buildx_prune.md#filter).

### <a name="format"></a> Format the output (--format)

The formatting options (`--format`) pretty-prints usage information output
using a Go template.

Valid placeholders for the Go template are:

* `.ID`
* `.Parents`
* `.CreatedAt`
* `.Mutable`
* `.Reclaimable`
* `.Shared`
* `.Size`
* `.Description`
* `.UsageCount`
* `.LastUsedAt`
* `.Type`

When using the `--format` option, the `du` command will either output the data
exactly as the template declares.

The `pretty` format is useful for inspecting the disk usage records in more
detail. It shows the mutable and shared states more clearly, as well as
additional information about the corresponding layer:

```console
$ docker buildx du --format=pretty
...
ID:           6wqu0v6hjdwvhh8yjozrepaof
Parents:
 - bqx15bcewecz4wcg14b7iodvp
Created at:   2025-06-12 15:44:02.715795569 +0000 UTC
Mutable:      false
Reclaimable:  true
Shared:       true
Size:         1.653GB
Description:  [build-base 4/4] COPY . .
Usage count:  1
Last used:    2 months ago
Type:         regular

Shared:         35.57GB
Private:        97.94GB
Reclaimable:    131.5GB
Total:          133.5GB
```

The following example uses a template without headers and outputs the
`ID` and `Size` entries separated by a colon (`:`):

```console
$ docker buildx du --format "{{.ID}}: {{.Size}}"
6wqu0v6hjdwvhh8yjozrepaof: 1.653GB
4m8061kctvjyh9qleus8rgpgx: 1.723GB
fcm9mlz2641u8r5eicjqdhy1l: 1.841GB
z2qu1swvo3afzd9mhihi3l5k0: 1.873GB
nmi6asc00aa3ja6xnt6o7wbrr: 2.027GB
0qlam41jxqsq6i27yqllgxed3: 2.495GB
3w9qhzzskq5jc262snfu90bfz: 2.617GB
```

The following example uses a `table` template and outputs the `ID` and
`Description`:

```console
$ docker buildx du --format "table {{.ID}}	{{.Descirption}}"
lu76wm07lk5u7fe9nul93o95o    [integration-tests 1/1] COPY . .
v6zmkcmgujv34vnys9eszttnv    [dev 1/1] COPY --link . .
nj4fwb6qxznswmij3fg30sns2    mount / from exec /bin/sh -c rpm-init $DISTRO_NAME
```

JSON output is also supported and will print as newline delimited JSON:

```console
$ docker buildx du --format=json
{"CreatedAt":"2025-07-29T12:36:01Z","Description":"pulled from docker.io/library/rust:1.85.1-bookworm@sha256:e51d0265072d2d9d5d320f6a44dde6b9ef13653b035098febd68cce8fa7c0bc4","ID":"ic1gfidvev5nciupzz53alel4","LastUsedAt":"2025-07-29T12:36:01Z","Mutable":false,"Parents":["hmpdhm4sjrfpmae4xm2y3m0ra"],"Reclaimable":true,"Shared":false,"Size":"829889526","Type":"regular","UsageCount":1}
{"CreatedAt":"2025-08-05T09:24:09Z","Description":"pulled from docker.io/library/node:22@sha256:3218f0d1b9e4b63def322e9ae362d581fbeac1ef21b51fc502ef91386667ce92","ID":"jsw7fx09l5zsda3bri1z4mwk5","LastUsedAt":"2025-08-05T09:24:09Z","Mutable":false,"Parents":["098jsj5ebbv1w47ikqigeuurs"],"Reclaimable":true,"Shared":true,"Size":"829898832","Type":"regular","UsageCount":1}
```

You can use `jq` to pretty-print the JSON output:

```console
$ docker buildx du --format=json | jq .
{
  "CreatedAt": "2025-07-29T12:36:01Z",
  "Description": "pulled from docker.io/library/rust:1.85.1-bookworm@sha256:e51d0265072d2d9d5d320f6a44dde6b9ef13653b035098febd68cce8fa7c0bc4",
  "ID": "ic1gfidvev5nciupzz53alel4",
  "LastUsedAt": "2025-07-29T12:36:01Z",
  "Mutable": false,
  "Parents": [
    "hmpdhm4sjrfpmae4xm2y3m0ra"
  ],
  "Reclaimable": true,
  "Shared": false,
  "Size": "829889526",
  "Type": "regular",
  "UsageCount": 1
}
{
  "CreatedAt": "2025-08-05T09:24:09Z",
  "Description": "pulled from docker.io/library/node:22@sha256:3218f0d1b9e4b63def322e9ae362d581fbeac1ef21b51fc502ef91386667ce92",
  "ID": "jsw7fx09l5zsda3bri1z4mwk5",
  "LastUsedAt": "2025-08-05T09:24:09Z",
  "Mutable": false,
  "Parents": [
    "098jsj5ebbv1w47ikqigeuurs"
  ],
  "Reclaimable": true,
  "Shared": true,
  "Size": "829898832",
  "Type": "regular",
  "UsageCount": 1
}
```

### <a name="verbose"></a> Use verbose output (--verbose)

Shorthand for [`--format=pretty`](#format):

```console
$ docker buildx du --verbose
...
ID:           6wqu0v6hjdwvhh8yjozrepaof
Parents:
 - bqx15bcewecz4wcg14b7iodvp
Created at:   2025-06-12 15:44:02.715795569 +0000 UTC
Mutable:      false
Reclaimable:  true
Shared:       true
Size:         1.653GB
Description:  [build-base 4/4] COPY . .
Usage count:  1
Last used:    2 months ago
Type:         regular

Shared:         35.57GB
Private:        97.94GB
Reclaimable:    131.5GB
Total:          133.5GB
```

### <a name="builder"></a> Override the configured builder instance (--builder)

Use the `--builder` flag to inspect the disk usage of a particular builder.

```console
$ docker buildx du --builder youthful_shtern
ID                                RECLAIMABLE    SIZE          LAST ACCESSED
g41agepgdczekxg2mtw0dujsv*        true           1.312GB       47 hours ago
e6ycrsa0bn9akigqgzu0sc6kr         true           318MB         47 hours ago
our9zg4ndly65ze1ccczdksiz         true           204.9MB       45 hours ago
b7xv3xpxnwupc81tc9ya3mgq6*        true           120.6MB       47 hours ago
zihgye15ss6vum3wmck9egdoy*        true           79.81MB       2 days ago
aaydharssv1ug98yhuwclkfrh*        true           79.81MB       2 days ago
ta1r4vmnjug5dhub76as4kkol*        true           74.51MB       47 hours ago
murma9f83j9h8miifbq68udjf*        true           74.51MB       47 hours ago
47f961866a49g5y8myz80ixw1*        true           74.51MB       47 hours ago
tzh99xtzlaf6txllh3cobag8t         true           74.49MB       47 hours ago
ld6laoeuo1kwapysu6afwqybl*        true           59.89MB       47 hours ago
yitxizi5kaplpyomqpos2cryp*        true           59.83MB       47 hours ago
iy8aa4b7qjn0qmy9wiga9cj8w         true           33.65MB       47 hours ago
mci7okeijyp8aqqk16j80dy09         true           19.86MB       47 hours ago
lqvj091he652slxdla4wom3pz         true           14.08MB       47 hours ago
fkt31oiv793nd26h42llsjcw7*        true           11.87MB       2 days ago
uj802yxtvkcjysnjb4kgwvn2v         true           11.68MB       45 hours ago
Reclaimable:    2.627GB
Total:          2.627GB
```
