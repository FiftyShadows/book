```
22189:C 14 Aug 21:33:09.084 * DB saved on disk
22189:C 14 Aug 21:33:09.084 * RDB: 0 MB of memory used by copy-on-write
8898:M 14 Aug 21:33:09.151 * Background saving terminated with success
8898:M 14 Aug 21:38:10.097 * 10 changes in 300 seconds. Saving...
8898:M 14 Aug 21:38:10.116 * Background saving started by pid 22197
22197:C 14 Aug 21:38:10.178 * DB saved on disk
22197:C 14 Aug 21:38:10.178 * RDB: 0 MB of memory used by copy-on-write
8898:M 14 Aug 21:38:10.216 * Background saving terminated with success
8898:M 14 Aug 21:43:11.083 * 10 changes in 300 seconds. Saving...
8898:M 14 Aug 21:43:11.102 * Background saving started by pid 22204
22204:C 14 Aug 21:43:11.103 # Write error saving DB on disk: No space left on device
```
是由于频繁写入,导致rdb文件过大塞满硬盘,无法写入.