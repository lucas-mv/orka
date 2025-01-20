# orka

orka is an open source community-based semi-federated social network project written in rust

## Main architecture

```mermaid
flowchart LR
    user@{ shape: circle, label: "Users" }
    client[Client]
    subgraph Main deployment
    main[Main orka node]
    main_db[(CassandraDB)]
    end
    subgraph Node deployment
    db[(PostgreSQL)]
    blob[(BLOB storage)]
    node@{ shape: procs, label: "Federated orka node"}
    end
    node --> db
    node --> blob
    main ---|HTTP| node
    user --> client --> main
    main --> main_db
```

Users should be able to use a client to connect to the main orka nodes, which handle operations like content moderation, user access, federation access, and handover the user to the federated orka nodes which handle the actual communities, posts, and user data. 