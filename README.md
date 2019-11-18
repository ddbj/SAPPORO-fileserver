# SAPPORO-fileserver

SAPPORO-fileserver is divided into file storage for input and file storage for output. File storage for input uses nginx. The file storage for output uses [Minio](https://www.minio.io), which is simple S3-compatible object storage.

[Japanese Document](https://hackmd.io/s/rJHpJwkdE)

## Usage

Using docker-compose

```shell
$ git clone git@github.com:ddbj/SAPPORO-fileserver.git
$ ./sapporo-fileserver up
Start SAPPORO-fileserver up...

  Access Key         : 9b92c81dc5dd16bb1977809ac68d553b
  Secret Access Key  : 095d71e5fe2f496f04e9f729090db9f5

Creating sapporo-fileserver-output ... done
Creating sapporo-fileserver-input  ... done

Please accsess in your brouwser:

    Input Server   -  http://localhost:1123/
    Output Server  -  http://localhost:1124/

Finish SAPPORO-fileserver up...
```

Start nginx and minio.

- nginx (Input Server): `http://localhost:1123/`
  - File root: `./data/input_data`
- minio (Output Server): `http://localhost:1124/`

---

```shell
$ ./sapporo-fileserver --help
sapporo-fileserver is a set of management commands for SAPPORO-fileserver.

Usage:
  sapporo-fileserver up [--access-key <KEY>] [--secret-access-key <KEY>]
  sapporo-fileserver show-keys
  sapporo-fileserver down
  sapporo-fileserver clean
  sapporo-fileserver input up
  sapporo-fileserver input down
  sapporo-fileserver input clean
  sapporo-fileserver output up [--access-key <KEY>] [--secret-access-key <KEY>]
  sapporo-fileserver output down
  sapporo-fileserver output clean

Option:
  -h, --help                  Print usage.
```

### Check minio keys

```shell
$ ./sapporo-fileserver show-keys
Start SAPPORO-fileserver show keys...
Minio Access Key: 9b92c81dc5dd16bb1977809ac68d553b
Minio Secret Key: 095d71e5fe2f496f04e9f729090db9f5
Finish SAPPORO-fileserver show keys...
```

### Down servers

```shell
$ ./sapporo-fileserver down
Start SAPPORO-fileserver down...
Stopping sapporo-fileserver-input  ... done
Stopping sapporo-fileserver-output ... done
Removing sapporo-fileserver-input  ... done
Removing sapporo-fileserver-output ... done
Network sapporo-network is external, skipping
Finish SAPPORO-fileserver down...
```

## Deployment

### Input server (nginx)

You can change the nginx settings. Edit `./etc/nginx/nginx.conf`.

---

You can download files from Host as `http://localhost:1123/file_path`

If you want to download files from another container that shares a docker external network as `http://sapporo-fileserver-input:8080/file_path`

### Output server (minio)

In browser, access to `localhost:1123`.

![Mineo Login](https://i.imgur.com/m1ghCUn.png)

Enter the Access Key and Secret Key set above.

![Minio Home](https://i.imgur.com/zKFAXzd.png)

---

Create a bucket. You can do this from the browser or from CLI.

The following is an example of using CLI.

```shell
$ docker-compose exec app mc config host add sapporo http://0.0.0.0:80 access_key secret_access_key
$ docker-compose exec app mc mb sapporo/sapporo
```

This will create a bucket called `sapporo`.

---

In Workflow Prepare, specify SAPPORO-fileserver instead of S3.

![Test](https://i.imgur.com/y0Tv7JZ.png)

---

The result is output as follows.

![Output](https://i.imgur.com/scZJbcm.png)

The entity of output exists under `SAPPORO/SAPPORO-fileserver/data/sapporo`.

```shell
$ ls ./output_data/sapporo
cwl_upload
$ ls ./output_data/sapporo/cwl_upload
small.ERR034597_1_fastqc.html  small.ERR034597_1.trimmed_fastqc.html  small.ERR034597_1.trimmed.fq
```
