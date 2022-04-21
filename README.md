# XMRig miner image for Monero
Monero (XMR) CPU miner packaged in a lightweight Docker image that you can easily deploy to a Kubernetes cluster.


## What is XMRig?
[XMRig](https://xmrig.com/miner) is a high performance, open source, cross platform RandomX, KawPow, CryptoNight and AstroBWT unified CPU/GPU miner and RandomX benchmark. It supports [Monero](https://www.getmonero.org/), among other cryptocurrencies. 

This Docker image was built with the latest XMRig version from source on Alpine Linux and it is only 16 MB in size. It's rootless, immutable, and supports your own donate level and timezone. You can easily deploy it as a standalone Docker container, or to a Kubernetes cluster in minutes.


## Quick reference
- **Maintained by**: [Roberto Meléndez](https://medium.com/@rcmelendez)
- **Supported architectures**: `amd64`, `arm32v7`, `arm64v8`
- **Supported tags**: `latest`, `6.17.0`, `6.16.4`, `6.16.3`, `6.16.2`


## How to use this image

**Step 1:** Clone the GitHub repo:
```
$ git clone https://github.com/rcmelendez/xmrig-docker.git
```

**Step 2:** Edit the [`config.json`](https://github.com/rcmelendez/xmrig-docker/blob/main/config.json) file. Provide your [pool](http://moneropools.com/), [wallet address](https://web.getmonero.org/downloads/), and coin to mine. If you are feeling generous, set the `donate-level` greater than 0:
```
"coin": "XMR",
"url": "gulf.moneroocean.stream:10128",
"user": "43BFSy88EBK7pstEvSkxp2BpnDYj2xP4PG4sf1MSywj2EDdF1WYyTysRGZFAh639zyKyZYzshQwQ4CELq9d76wob3zwfGuc",
"donate-level": 0,
```
For all the available options, visit [XMRig Config File](https://xmrig.com/docs/miner/config) documentation. 

**Step 3:** Deploy the image as a standalone Docker container or to a Kubernetes cluster.

### Docker
```
$ docker run -dit --rm \
    --volume "$(pwd)"/config.json:/xmrig/etc/config.json:ro \
    --volume "$(pwd)"/log:/xmrig/log \
    --name xmrig rcmelendez/xmrig \
    xmrig --config=/xmrig/etc/config.json
```
If you prefer **Docker Compose**, edit the [`docker-compose.yml`](https://github.com/rcmelendez/xmrig-docker/blob/main/docker-compose.yml) manifest as needed and run:
```
$ docker-compose up -d
```

### Kubernetes
**Step 1:** Create a *namespace* for our XMRig application (optional but recommended):
```
$ kubectl create ns xmrig
``` 
**Step 2:** Create a *configmap* in the new namespace `xmrig` from the [`config.json`](https://github.com/rcmelendez/xmrig-docker/blob/main/config.json) file:
```
$ kubectl create configmap xmrig-config --from-file config.json -n xmrig
```
**Step 3:** Edit the [`xmrig.yaml`](https://github.com/rcmelendez/xmrig-docker/blob/main/xmrig.yaml) file. Things you may want to modify include:
- `replicas`: number of desired pods to be running.
- `image:tag`: to view all available versions, go to the [Tags](https://hub.docker.com/r/rcmelendez/xmrig/tags) tab of the Docker Hub repo.
- `resources`: set appropriate values for `cpu` and `memory` requests/limits.
- `affinity`: the manifest will schedule only one pod per node, if that's not the desired behavior, remove the `affinity` block.

**Step 4:** Once you are satisfied with the above manifest, create a *deployment*:
```
$ kubectl -f apply xmrig.yaml
```


## Logging
This Docker image sends the container logs to the `stdout`. To view the logs, run:

```
$ docker logs xmrig
```

For Kubernetes run:
```
$ kubectl logs -n xmrig <pod-name> 
```
### Persistent logging
Containers are stateless by nature, so their logs will be lost when they shut down. If you want the logs to persist, enable XMRig syslog output in the [`config.json`](https://github.com/rcmelendez/xmrig-docker/blob/main/config.json) file: 
```
"syslog": true,
"log-file": "/xmrig/log/xmrig.log",
```
And give full permissions to the directory on the host machine:
```
$ chmod 777 "$(pwd)"/log
```

Then use either **Docker** [bind mounts](https://docs.docker.com/storage/bind-mounts/) or **Kubernetes** [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) to keep the log file on the host machine. The `docker run` command above and the [`docker-compose.yml`](https://github.com/rcmelendez/xmrig-docker/blob/main/docker-compose.yml) file already includes this mapping. 


## Disclaimer
None of this can be considered financial advice. Do your own research and mine the cryptocurrencies that you prefer. I'm a tech enthusiast with foundational knowledge of Docker and Kubernetes. This project was inspired by my curiosity to start learning about the cryptocurrency world and by the drive to keep improving my tech skills. Will I or you become rich by using XMRig? I don't think so. Will be it fun? Yeah. Will you learn? A lot!


## License
The Docker image is licensed under the terms of the [MIT License](https://github.com/rcmelendez/xmrig-docker/blob/main/LICENSE). XMRig is licensed under the GNU General Public License v3.0. See its [`LICENSE`](https://github.com/xmrig/xmrig/blob/master/LICENSE) file for details.


## Contact 
Find me as __rcmelendez__ on [LinkedIn](https://www.linkedin.com/in/rcmelendez/), [Medium](https://medium.com/@rcmelendez), and [GitHub](https://github.com/rcmelendez/).
