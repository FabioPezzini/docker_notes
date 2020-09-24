## Docker Swarm architecture
At high level we can summarize the Swarm architecture in [two main parts](https://prnt.sc/uj5bnk). A raft consensus group of an odd number of `manager nodes`, and a group of `worker nodes` that communicate with each other over a gossip network, also called the control plane.
The `manager nodes` manage the swarm while the worker nodes execute the applications deployed into the swarm. Each manager has a complete copy of the full state of the Swarm in its local raft store. Managers synchronously communicate with each other and their raft stores are always in sync.
The `workers`, on the other hand, communicate with each other asynchronously for scalability reasons. There can be hundreds if not thousands of worker nodes in a Swarm.

NB = we ca classify a node as a VM or as a physical computer.

### Swarm managers
Each Docker Swarm needs to include at least one manager node. For high availability reasons, we should have more than one manager node in a Swarm. The `Raft consensus protocol` is a standard protocol that is often used when multiple entities need to work together and always need to agree with each other as to which activity to execute next (used by managers node).
In the case of Docker Swarm, the first node that starts the Swarm initially becomes the leader. If the leader goes away then the remaining manager nodes elect a new leader (between the managers).
Since all manager follower nodes have to communicate synchronously with the leader node to make a decision in the cluster, the decision-making process gets slower and slower the more manager nodes we have forming the consensus group, in fact whenever the consensus group needs to make a decision, the leader asks all followers for agreement.
All of the Swarm states are stored in a high-performance `key-value` store (kv-store) on each manager node. That's right, each manager node stores a complete replica of the whole Swarm state. This redundancy makes the Swarm highly available. If a manager node goes down, the remaining managers all have the complete state at hand.

### Swarm workers
Worker nodes communicate with each other over the so-called control plane. They use the `gossip protocol` for their communication. This communication is asynchronous, which means that, at any given time, it is likely that not all worker nodes are in perfect sync. The informations that they exchange are about service discovering and routing.
Worker nodes are kind of passive. They never actively do something other than run the workloads that they get assigned by the manager nodes. The worker makes sure, though, that it runs these workloads to the best of its capabilities.



## Stacks, services and tasks
We don't care about individual containers and where they are deployed any
longer—we just care about having a desired state defined through a service.

### Services
A Swarm service is an abstract thing. It is a description of the desired state of an application or application service that we want to run in a Swarm. The Swarm service is like a manifest describing:
- Name of the service
- Image from which to create the containers
- Numbers of replicas to run
- Network(s) that the containers of the service are attached to
- Ports that should be mapped

### Task
Part of that description was the number of replicas the service should be running. Each replica is represented by a task. In this regard, a Swarm service contains a collection of tasks. Each task of a service is deployed by the Swarm scheduler to a worker node. The task contains all of the necessary information that the worker node needs to run a
container based on the image, which is part of the service description

### Stack
A stack is used to describe a collection of Swarm services that are
related, most probably because they are part of the same application. In that sense, we could also say that a stack describes an application that consists of one to many services that we want to run on the Swarm.
Typically, we describe a stack declaratively in a text file that is formatted using the YAML format and that uses the same syntax as the already-known Docker Compose file.
The relationship can be summarized by this [image](https://prnt.sc/un1b2t).