<!-- TITLE: Docker Swarm -->
<!-- SUBTITLE: My references about Docker Swarm -->

# Containers Everywhere = New Problems
- How do we automate container lifecycle?
- How can we easily scale out/in/up/down?
- How can we ensure our containers are re-created if they fail?
- How can we replace containers without downtime (blue/green
deploy)?
- How can we control/track where containers get started?
- How can we create cross-node virtual networks?
- How can we ensure only trusted servers run our containers?
- How can we store secrets, keys, passwords and get them to the right
container (and only that container)?

# Swarm Mode: Built-In Orchestration
- Swarm Mode is a clustering solution built inside Docker
- Not related to Swarm "classic" for pre-1.12 versions
- Added in 1.12 (Summer 2016) via SwarmKit toolkit
- Enhanced in 1.13 (January 2017) via Stacks and Secrets
- Not enabled by default, new commands once enabled
    - docker swarm
    - docker node
    - docker service
    - docker stack
    - docker secret
# Swarm Architecture
![Swarm Mode](/uploads/swarm/swarm-mode.png "Swarm Mode")

![Swarm Topology](/uploads/swarm/swarm-topology.png "Swarm Topology")

![Nginx On Swarm](/uploads/swarm/nginx-on-swarm.png "Nginx On Swarm")

![Swarm Architecture](/uploads/swarm/swarm-architecture.png "Swarm Architecture")
# Swarm Initialization
`docker info | grep Swarm` See whether swarm is active
`docker swarm init` Activate swarm mode