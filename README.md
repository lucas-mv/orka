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

### PostgreSQL data structure

|orka_users|type|comment|
|-|-|-|
|id |UUID|PK|
|handle|string||
|bio|string||
|created_at|datetime||
|updated_at|datetime||

|orka_posts|type|comment|
|-|-|-|
|id|UUID|PK|
|user_id|UUID|FK orka_users[id]|
|community_id|UUID|FK orka_communities[id]|
|created_at|datetime||
|text|datetime||

|orka_communities|type|comment|
|-|-|-|
|id|UUID|PK|
|description|string||
|created_at|datetime||
|updated_at|datetime||

|orka_user_communities|type|comment|
|-|-|-|
|user_id|UUID|PK(user_id + community_id)|
|community_id|UUID||
|role|enum|owner, moderator, member|
|joined_at|datetime||
|banned_until|datetime|nullable|
|created_at|datetime||
|updated_at|datetime||

### Cassandra data structure