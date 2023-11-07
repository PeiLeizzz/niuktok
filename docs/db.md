# 数据库设计

- 所有表中都有的字段

|    Column    |    Type    | Unique | Not Null |      Default      | Foreign Key | Primary Key |    note     |
| :----------: | :--------: | :----: | :------: | :---------------: | :---------: | :---------: | :---------: |
| created_time | timestamp  |        |    √     | CURRENT_TIMESTAMP |             |             |             |
| updated_time | timestamp  |        |    √     | CURRENT_TIMESTAMP |             |             | auto update |
| deleted_time | timestamp  |        |          |       NULL        |             |             |             |
|  is_deleted  | tinyint(1) |        |    √     |         0         |             |             |             |

## user

|    Column    |        Type         | Unique | Not Null |      Default       | Foreign Key | Primary Key |                  note                  |
| :----------: | :-----------------: | :----: | :------: | :----------------: | :---------: | :---------: | :------------------------------------: |
|      id      |   unsigned bigint   |   √    |    √     |                    |             |      √      |            雪花算法生成 ID             |
|   username   |    varchar(255)     |   √    |    √     |                    |             |             |                                        |
|    avatar    |    varchar(1024)    |        |    √     |    Default URL     |             |             |                                        |
|     sex      | unsigned tinyint(2) |        |          |        NULL        |             |             |                                        |
| followed_num |   unsigned bigint   |        |    √     |         0          |             |             |                关注数量                |
| follower_num |   unsigned bigint   |        |    √     |         0          |             |             |                粉丝数量                |
|    status    | unsigned tinyint(2) |        |    √     | USER_STATUS_NORMAL |             |             |    用户状态（正常、封禁等）（可选）    |
|     ...      |                     |        |          |                    |             |             | 生日、签名、注册时间等其他信息（可选） |

- 关注、粉丝数量可以定时异步更新

## user_follow

|   Column    |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :---------: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|     id      | unsigned bigint |   √    |    √     |         |             |      √      |      |
|   user_id   | unsigned bigint |        |    √     |         |   user.id   |             |      |
| follower_id | unsigned bigint |        |    √     |         |   user.id   |             |      |

- `unique index(user_id, follower_id, delete_time)`

## user_auth

|    Column     |        Type         | Unique | Not Null | Default | Foreign Key | Primary Key |                   note                   |
| :-----------: | :-----------------: | :----: | :------: | :-----: | :---------: | :---------: | :--------------------------------------: |
|      id       |   unsigned bigint   |   √    |    √     |         |             |      √      |                                          |
|    user_id    |   unsigned bigint   |   √    |    √     |         |   user.id   |             |                                          |
| identity_type | unsigned tinyint(4) |        |    √     |         |             |             | 登陆类型（手机号 / 邮箱 / 用户名等方式） |
|  identifier   |    varchar(255)     |   √    |    √     |         |             |             |   登陆标识（手机号 / 邮箱 / 用户名等）   |
|  credential   |     varchar(32)     |        |    √     |         |             |             |                 密码凭证                 |

- 验证码登陆方式可以不存数据库，使用 Redis 即可（绑定手机号 - 验证码 - 下次允许的发送时间）
- `unique index(user_id, identity_type, delete_time)`

## video

|    Column    |      Type       | Unique | Not Null |   Default   | Foreign Key | Primary Key |                     note                     |
| :----------: | :-------------: | :----: | :------: | :---------: | :---------: | :---------: | :------------------------------------------: |
|      id      | unsigned bigint |   √    |    √     |             |             |      √      |               雪花算法生成 ID                |
|   user_id    | unsigned bigint |   √    |    √     |             |   user.id   |             |                  视频发布者                  |
|    title     |  varchar(255)   |        |    √     |             |             |             |                     标题                     |
| description  |  varchar(1024)  |        |          |    NULL     |             |             |                   视频描述                   |
|  video_key   |  varchar(128)   |   √    |    √     |             |             |             |             视频 Key（七牛提供）             |
|  video_path  |  varchar(1024)  |        |    √     |             |             |             |                   视频路径                   |
|  cover_path  |  varchar(1024)  |        |    √     | Default URL |             |             |                 视频封面路径                 |
|   view_num   | unsigned bigint |        |    √     |      0      |             |             |                    播放量                    |
|   like_num   | unsigned bigint |        |    √     |      0      |             |             |                    点赞量                    |
| favorite_num | unsigned bigint |        |    √     |      0      |             |             |                    收藏量                    |
|  share_num   | unsigned bigint |        |    √     |      0      |             |             |                    转发量                    |
|     info     |      json       |        |    √     |             |             |             | 视频元数据（七牛提供？分辨率、时长、大小等） |
| partition_id |      Long       |        |    √     |             |             |             |                   分区 ID                    |

- 播放、点赞、收藏、转发量可以定时异步更新
- `unique_index(video_key, deleted_time)`

## video_partition

| Column |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :----: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|   id   | unsigned bigint |   √    |    √     |         |             |      √      |      |
|  name  |   varchar(64)   |   √    |    √     |         |             |             |      |

## video_tag

| Column |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :----: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|   id   | unsigned bigint |   √    |    √     |         |             |      √      |      |
| title  |   varchar(64)   |   √    |    √     |         |             |             |      |

## video_tag_link

|  Column  |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :------: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|    id    | unsigned bigint |   √    |    √     |         |             |      √      |      |
|  tag_id  | unsigned bigint |        |    √     |         |   tag.id    |             |      |
| video_id | unsigned bigint |        |    √     |         |  video.id   |             |      |

- `unique index(tag_id, video_id, delete_time)`

## user_video_view

|    Column     |       Type       | Unique | Not Null | Default | Foreign Key | Primary Key |              note              |
| :-----------: | :--------------: | :----: | :------: | :-----: | :---------: | :---------: | :----------------------------: |
|      id       | unsigned bigint  |   √    |    √     |         |             |      √      |                                |
|    user_id    | unsigned bigint  |        |    √     |         |   user.id   |             |                                |
|   video_id    | unsigned bigint  |        |    √     |         |  video.id   |             |                                |
|     count     | unsigned int(11) |        |    √     |    0    |             |             |            观看次数            |
| last_progress | unsigned int(11) |        |    √     |         |             |             | 上次观看的进度（记录到第几秒） |

- `unique index(user_id, video_id, delete_time)`

## user_video_favorite

|  Column  |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :------: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|    id    | unsigned bigint |   √    |    √     |         |             |      √      |      |
| user_id  | unsigned bigint |        |    √     |         |   user.id   |             |      |
| video_id | unsigned bigint |        |    √     |         |  video.id   |             |      |

- 可选（每个用户有多个收藏夹）
- `unique index(user_id, video_id, delete_time)`

## user_video_like

|  Column  |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :------: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|    id    | unsigned bigint |   √    |    √     |         |             |      √      |      |
| user_id  | unsigned bigint |        |    √     |         |   user.id   |             |      |
| video_id | unsigned bigint |        |    √     |         |  video.id   |             |      |

- `unique index(user_id, video_id, delete_time)`

## user_video_share

|  Column  |       Type       | Unique | Not Null | Default | Foreign Key | Primary Key |   note   |
| :------: | :--------------: | :----: | :------: | :-----: | :---------: | :---------: | :------: |
|    id    | unsigned bigint  |   √    |    √     |         |             |      √      |          |
| user_id  | unsigned bigint  |        |    √     |         |   user.id   |             |          |
| video_id | unsigned bigint  |        |    √     |         |  video.id   |             |          |
|  count   | unsigned int(11) |        |    √     |    0    |             |             | 观看次数 |

- `unique index(user_id, video_id, delete_time)`

## comment

|      Column       |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key |               note               |
| :---------------: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :------------------------------: |
|        id         | unsigned bigint |   √    |    √     |         |             |      √      |                                  |
|   from_user_id    | unsigned bigint |        |    √     |         |   user.id   |             |                                  |
|    to_user_id     | unsigned bigint |        |          |  NULL   |   user.id   |             |       为 NULL 则不是楼中楼       |
|     video_id      | unsigned bigint |        |    √     |         |  video.id   |             |                                  |
| father_comment_id | unsigned bigint |        |          |  NULL   |             |             |       为 NULL 则不是楼中楼       |
|      content      |  varchar(1024)  |        |    √     |         |             |             | （可选：支持图片、表情、语音等） |
|     like_num      | unsigned bigint |        |    √     |    0    |             |             |             点赞数量             |

- 点赞数量可以定时异步更新

## user_comment_like

|   Column   |      Type       | Unique | Not Null | Default | Foreign Key | Primary Key | note |
| :--------: | :-------------: | :----: | :------: | :-----: | :---------: | :---------: | :--: |
|     id     | unsigned bigint |   √    |    √     |         |             |      √      |      |
|  user_id   | unsigned bigint |        |    √     |         |   user.id   |             |      |
| comment_id | unsigned bigint |        |    √     |         | comment.id  |             |      |

- `unique index(user_id, comment_id, delete_time)`

## message

|    Column    |        Type         | Unique | Not Null | Default  | Foreign Key | Primary Key |                           note                           |
| :----------: | :-----------------: | :----: | :------: | :------: | :---------: | :---------: | :------------------------------------------------------: |
|      id      |   unsigned bigint   |   √    |    √     |          |             |      √      |                                                          |
| from_user_id |   unsigned bigint   |        |          |   NULL   |   user.id   |             |                   为 NULL 则是系统消息                   |
|  to_user_id  |   unsigned bigint   |        |    √     |          |   user.id   |             |                                                          |
|   content    |    varchar(1024)    |        |    √     |          |             |             |             （可选：支持图片、表情、语音等）             |
|     type     | unsigned tinyint(2) |        |    √     |          |             |             | 消息类型（系统通知、纯文字消息、富文本消息、语音消息等） |
|   is_read    | unsigned tinyint(2) |        |    √     | UNREADED |             |             |                         是否已读                         |
