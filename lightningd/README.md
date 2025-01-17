## Lightningd
The Dockerfile was adapted from https://github.com/ElementsProject/lightning/blob/master/Dockerfile.

Feel free to adapt the `build-and-push-to-dockerhub.sh` to push to your own repo/registry.

You can add custom plugins in the [Dockerfile](./Dockerfile#L29), just follow the way the Prometheus plugin has been added. 

### How to run
```
/etc/systemd/system/lightning.service
[Unit]
Description=Lightning node
Wants=docker.target
After=docker.service

[Service]
Restart=always
RestartSec=3
ExecStartPre=/usr/bin/docker pull blockstream/lightningd:latest
ExecStart=/usr/bin/docker run \
    --network=host \
    --pid=host \
    --name=lightning \
    -v /mnt/data/lightning/lightning.conf:/root/.lightning/lightning.conf:ro \
    -v /mnt/data/lightning:/root/.lightning \
    blockstream/lightningd:latest lightningd --testnet --conf=/root/.lightning/lightning.conf --plugin-dir=/usr/local/bin/plugins
ExecStop=/usr/bin/docker exec lightning lightning-cli stop
ExecStopPost=/usr/bin/sleep 3
ExecStopPost=/usr/bin/docker rm -f lightning
```
