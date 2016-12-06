# PSQL FROM S3_FILE
A small utility to execute sql-commands from a file that resides in S3.
Can log to a file that

## Requirements

Set env-var PGURL for psql connection (form: `postgresql://[user[:password]@][netloc][:port][/dbname][?param1=value1&...]`):
  * PGURL
(and if need be, other PG env vars according to your needs)

and these for AWS CLI (or less if using EC2-roles) (see [AWS CLI ENV-vars](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-environment)):
  * AWS_ACCESS_KEY_ID
  * AWS_SECRET_ACCESS_KEY
  * AWS_DEFAULT_REGION

## Temp-volume

Be sure to bind a temporary volume to `/sqltemp` (or somewhere else, but set env `TEMP_SQl_DIR` to that).

## Command
Entrypoint is the script [execute_psql](execute_sql).

The script takes these parameters:
    `s3://mybucket/myfolder/my_sql_script.sql` and optional a log destination `s3://mybucket/mylogfolder/my_sql_script.log`


## psql parameters

Utility will use these parameters for psql :
`psql "${PG_URL}" --file=<file from S3> --output=/sqltemp/output.log`

explanied as (from `man psql`):
```
  --file=filename
           Use the file filename as the source of commands instead of reading commands interactively. After the file is processed, psql terminates. This is in many ways equivalent to the meta-command \i.

           If filename is - (hyphen), then standard input is read until an EOF indication or \q meta-command. Note however that Readline is not used in this case (much as if -n had been specified).

           Using this option is subtly different from writing psql < filename. In general, both will do what you expect, but using -f enables some nice features such as error messages with line numbers. There
           is also a slight chance that using this option will reduce the start-up overhead. On the other hand, the variant using the shell's input redirection is (in theory) guaranteed to yield exactly the
           same output you would have received had you entered everything by hand.
  --output=filename
           Put all query output into file filename. This is equivalent to the command \o
```
## Example

`docker run --rm -it -e PGURL=xx AWS_DEFAULT_REGION=eu-west-1 -e AWS_ACCESS_KEY_ID=xx -e AWS_SECRET_ACCESS_KEY=xx -v /sqltemp upptec/psql_from_s3_fle s3://my_bucket/my_folder/my_sql_script.sql`
