Fluent Bit configuration file sets up the service to read logs from various sources, **parse** them, and ==send the processed logs to specified destinations==. Here's a detailed explanation of each section:

- Service Section

```plaintext
[SERVICE]
    Parsers_File /etc/fluent-bit/parsers.conf
```

**Parsers_File**: Specifies the location of the parsers configuration file (`/etc/fluent-bit/parsers.conf`). This file defines how logs are parsed based on different formats.

- ### Input Sections: 
These sections define where Fluent Bit reads log files from.

```plaintext
[INPUT]
    name tail
    tag  nginx
    path {{ cdn_log_dir }}/*.log
    parser simple_json

```