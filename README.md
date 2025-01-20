# orka

orka is an open source community-based semi-federated social network project written in rust

## Main architecture

```mermaid
flowchart LR
    user@{ shape: circle, label: "Users" }
    client[Client]
    subgraph Main instace
    main[Main orka node]
    main_db[(CassandraDB)]
    end
    subgraph Node instances
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

|orka_instance|type|comment|
|-|-|-|
|instance_host|string|PK|
|instance_password_hash|string||
|instance_password_salt|UUID||
|instance_pepper|UUID||

|orka_users|type|comment|
|-|-|-|
|id |UUID|PK|
|handle|string||
|role|enum|instance_admin, instance_moderator, instance_user|
|name|string||
|bio|string||
|password_hash|string||
|salt|UUID||
|created_at|datetime||
|updated_at|datetime||

|orka_posts|type|comment|
|-|-|-|
|id|UUID|PK|
|user_id|UUID|FK orka_users[id]|
|community_id|UUID|FK orka_communities[id]|
|thread_id|UUID||
|orginal_thread_post|bool||
|flags|int64|int bit style content flags (removed reason, type of content, general warnings, etc)|
|text|string||
|images_attached|int8|number of images associated with post (view blob storage section)|
|video_attached|bool||
|created_at|datetime||

|orka_communities|type|comment|
|-|-|-|
|id|UUID|PK|
|name|string||
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

|orka_user_follows|type|comment|
|-|-|-|
|id|UUID|PK|
|user_id|UUID|FK orka_users[id]|
|follow_id|UUID||
|follow_instance_id|string||
|mutual_follow|bool||
|created_at|datetime||
|updated_at|datetime||

### Cassandra data structure

|instances|type|comment|
|-|-|-|
|instance_host|string|key|
|name|string|key|
|description|string||
|owner_user_id|UUID||
|owner_email|string||
|owner_secondary_contact|string||
|legal_information|string||
|blocked_until|datetime|nullable|
|federation_status|enum|federated, banned, deleted|
|support_history|list[string]||
|moderation_strikes|int8|3 strikes and you're out|
|created_at|datetime||
|updated_at|datetime||

|users|type|comment|
|-|-|-|
|id|UUID|key|
|name|string||
|bio|string||
|password_hash|string||
|salt|string||
|role|enum|admin, moderator, support|
|created_at|datetime||
|updated_at|datetime||

