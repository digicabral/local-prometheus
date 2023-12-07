1 - Install prometheus using docker by
`docker run -p 9090:9090 prom/prometheus`

2 - Create a prometheus.yml file in the folder.

```global:
scrape_interval: 30s
evaluation_interval: 25s

scrape_configs:

- job_name: "prometheus"
  static_configs:
  - targets: ["localhost:9090"]`
```

3 - Bind-mount your prometheus.yml from the host by running:

```
docker run \
    -p 9090:9090 \
    -v ./prometheus.yml:/etc/prometheus/prometheus.yml \
    prom/prometheus
```

4 - Create a persistent volume
`docker volume create prometheus-volume`

5 - Run Prometheus

```
docker run --rm --detach \
    --name my-prometheus \
    --publish 9090:9090 \
    --volume prometheus-volume:/prometheus \
    --volume "$(pwd)"/prometheus.yml:/etc/prometheus/prometheus.yml \
    prom/prometheus
```

Here's what the command does:

- Spin up an instance of the prom/prometheus Docker container image in detached mode (so you can continue using your terminal session).
- The --rm flag tells Docker to delete the stopped image when you finish working with it. This is only suitable for development/testing purposes and helps you automatically clean up containers that you're no longer using.
- The first --volume command tells Prometheus to store its metrics data in the Docker persistent volume we created earlier.
- The second --volume command passes in our Prometheus configuration file by placing it in the container's file system so that Prometheus uses it when it starts up.
- The --publish command exposes the container port 9090 to our local machine so that we can access Prometheus in a web browser.

6 - Check if it's running: [http://localhost:9090](http://localhost:9090)

7 - Shutting down and free up the port: `docker kill my-prometheus`
