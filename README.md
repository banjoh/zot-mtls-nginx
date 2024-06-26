### Archived in favour of https://github.com/banjoh/docker-registry

# Local image registry
Simple docker example to run a [registry](https://distribution.github.io/distribution/) with nginx reverse proxy configured with mtls. This setup is based on https://distribution.github.io/distribution/recipes/nginx/ instructions. Its not meant to be used in live systems. You can use it to test a registry running locally for instance.

### Dependencies
- docker
- make
- bash
- openssl

### Usage
- Start the docker container using the commands below
```sh
make run
```

- Simple test using `curl`. Expect a `301` header
```sh
curl -L --cert certs/client.crt --key certs/client.key --cacert certs/ca.crt https://localhost/v2 -I
HTTP/1.1 301 Moved Permanently
...
```

- Push a helm chart using this command
```sh
helm create foo
helm package foo/.
docker login localhost:5000 -u registry -p password
docker login localhost:443 -u registry -p password
helm push foo-0.1.0.tgz oci://localhost:443 --ca-file certs/ca.crt --cert-file certs/client.crt --key-file certs/client.key
helm push foo-0.1.0.tgz oci://localhost:5000
```

- Clean up
```sh
make clean
```
