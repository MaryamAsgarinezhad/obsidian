Fluent Bit configuration file sets up the service to read logs from various sources, **parse** them, and ==send the processed logs to specified destinations==. Here's a detailed explanation of each section:

- **Service Section**

```plaintext
[SERVICE]
    Parsers_File /etc/fluent-bit/parsers.conf
```

**Parsers_File**: Specifies the location of the parsers configuration file (`/etc/fluent-bit/parsers.conf`). This file defines how logs are parsed based on different formats.

- ### Input Sections:  These sections define where Fluent Bit reads log files from.

1-Nginx Access Logs

```plaintext
[INPUT]
    name tail
    tag  nginx
    path {{ cdn_log_dir }}/*.log
    parser simple_json
```
- **name**: Specifies the input plugin to use (`tail`), which reads log files.
- **tag**: Assigns a tag (`nginx`) to the logs read from these files.
- **path**: The location of the log files to read (using the `cdn_log_dir` variable).
- **parser**: Specifies the parser to use (`simple_json`) to interpret the log content.

2- Other log file types are Nginx Error Logs, Ubuntu System Logs, Ubuntu Audit Logs, ... . Tag, path and parser will change accordingly.


- ### Output Sections: These sections define where Fluent Bit sends the processed logs.

1- Forward Output for Nginx Access Logs

```plaintext
[OUTPUT]
    name  forward
    match nginx
    host 172.16.0.6
    port 32502
    tag sre_boom_app
    shared_key e5c2d2f01cbb2d4a3c75
```

- **name**: Specifies the output plugin to use (`forward`), which forwards logs to another Fluent Bit or Fluentd instance.
- **match**: Matches logs tagged with `nginx`.
- **host**: The destination host IP address (`172.16.0.6`).
- **port**: The destination port (`32502`).
- **tag**: Assigns a new tag (`sre_boom_app`) to the forwarded logs.
- **shared_key**: Authentication key used for secure forwarding.