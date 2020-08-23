## PHP Checklist

### Code & Command Execution
- eval
- create_function
- assert
- preg_replace
- shell_exec
- exec
- system
- popen
- passthru
- call_user_func
- call_user_func_array
- call_user_method
- call_user_method_array
- usort
- array_map
- array_filter
- array_reduce
- array_walk
- array_walk_recursive
- pcntl_exec
- proc_open
- mail
- include
- include_once
- require
- require_once
- putenv
- unserialize
- ``
- "${phpinfo()}"
- dl

### SSRF
- curl_exec
- stream_socket_client
- socket_connect
- fsockopen
- fopen

### XXE
- XMLReader
- xml_parse
- xml_parse_into_struct 
- xml_parser_create_ns 
- simplexml_load_file 
- simplexml_load_string
- SimpleXMLElement
- DOMDocument

### Parameter Override
- extract
- parse_str
- import_request_variables
- $$

### File
- fputcsv
- fputs
- fprintf
- fwrite
- file_put_contents
- move_uploaded_file
- copy
- rename
- link
- symlink
- fopen
- readfile
- file
- file_get_contents
- unlink
- readdir
- glob
- mkdir
- rmdir

### Open Redirect
- header

### XSS
- echo
- print
- printf
- print_r
- var_dump
- var_export

### Information Disclosure
- phpinfo

### SQL Injection
- mysql_query
- mysql_db_query
- mysql_fetch_array
- mysql_fetch_assoc 
- mysql_fetch_field 
- mysql_fetch_lengths 
- mysql_fetch_object
- mysql_fetch_row 
- mysql_unbuffered_query
- PDO::query 
- mysqli_query
