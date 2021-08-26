# update based on select

## Refrences

> https://stackoverflow.com/questions/1262786/mysql-update-query-based-on-select-query

```
CREATE TABLE `u_player` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `uid` int(10) unsigned NOT NULL,
  `gid` int(10) unsigned NOT NULL,
  `lid` int(10) unsigned NOT NULL,
  `admin_id` int(10) unsigned NOT NULL,
  `team_id` int(10) unsigned NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `u_player_uid_gid_unique` (`uid`,`gid`)
) ENGINE=InnoDB;
```

```
CREATE TABLE `p_order` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `money` decimal(8,2) unsigned NOT NULL COMMENT '订单金额',
  `gid` int(10) unsigned NOT NULL,
  `team_id` int(10) unsigned NOT NULL,
  `admin_id` int(10) unsigned NOT NULL,
  `uid` int(10) unsigned NOT NULL,
  KEY `p_order_uid_gid_index` (`uid`,`gid`),
) ENGINE=InnoDB COMMENT='订单';
```

```
UPDATE `p_order` o
INNER JOIN u_player p ON o.uid = p.uid
AND o.gid = p.gid
SET o.`admin_id` = p.admin_id,
 o.team_id = p.team_id
```

