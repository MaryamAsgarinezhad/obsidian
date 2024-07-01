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

