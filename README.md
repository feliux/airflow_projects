# Airflow

Build pipelines and workflows with Apache Airflow.

## Deployment

Deploy services running the following command `$ docker-compose up -d`

Services availables on the following ports

- Airflow -> `localhost:8080`

- Minio -> `localhost:9000`

## Usage

### Shell

- Airflow help: `$ airflow -h`

- Run a DAG: `$ airflow run $dag_id $task_id $date_execution`

- Run a full DAG: `$ airflow trigger_dag -e $date_execution $dag_id`

- Start webserver on port 9090: `$ airflow webserver -p 9090`

### Operators

- Airflow operators

~~~
from airflow.operators.dummy_operator import DummyOperator
from airflow.operators.bash_operator import BashOperator
from airflow.operators.python_operator import PythonOperator
from airflow.operators.http_operator import SimpleHttpOperator
from airflow.operators.email_operator import EmailOperator
~~~

### Sensors

Use a sensor when

1. A condition will be true.

2. If you want to continue to check for a condition but not necessarily fail the entire DAG inmediately.

3. To add task repetition without loops in your DAG.

**Sensor types**

- `FileSensor`. Check for the existence of a file at a certain location. Requires `filepath` argument and might need `mode` or `poke_interval` attributes.

- `ExternalTaskSensor`. Wait for a task in another DAG to complete.

- `HttpSensor`. Request a web URL and check for content.

- `SqlSensor`. Runs a SQL query to check for content.

- Many others in `airflow.sensors` and `airflow.contrib.sensors`

### Executors

Is the component that actually runs the tasks defined within your workflows. You can create your own executors.

**Executors types**

You can determine your executor via the `airflow.cfg` file, just look for the `executor=` line. Another option is wit the `airflow list_dags` command.

- `SequentialExecutor`. The default executor. Only run a task at a time. Useful for debugging. It is very functional but not really recommended for production.

- `LocalExecutor`. Runs enterely on a single system. Treats tasks as processes and is able to start as many concurrent tasks as desired/permitted by the system resources. *Parallelism* defined by the user.

- `CeleryExecutor`. Uses Celery (general queuing system written in Python that allows multiple systems to communicate as a basic cluster) backend as task manager. Multiple worker systems can be defined. Is significantly more difficult to setup/configure.

### Templates

Templates allow substitution of information during a DAG run. Provide added flexibility when defining tasks. Writen using `jinja` language.

### Branching

Use branching to add conditional logic tou your DAGs.

~~~
from airflow.operators.python_operator import BranchPythonOperator
~~~

## Troubleshooting

- Check if scheduler is running. Fix by running `airflow scheduler`

- If DASs will not run on schedule, maybe at least one `schedule_interval` has not passed. Also, could not be enough tasks free within the executor run.

- If DAGs do not load, check if the DAG is in web UI. check if it is `airflow list_dags`. Verify DAG file is in correct folder

## Extra

### Cron syntax

~~~
----------- minute (0 - 59)
| --------- hour (0 - 23)
| | ------- day of the month (1 - 31)
| | | ----- month (1 - 12)
| | | | --- day of the week (0 - 6) (Sunday to Saturday)
| | | | |
| | | | |
| | | | |
* * * * * command to execute

Examples:

0 12 * * *          # run daily at noon
* * 25 2 *          # run once per minute on February 25
0,15,30,45 * * * *  # run every 15 minutes
~~~

- Presets
    - @hourly == 0 * * * *
    - @daily == 0 0 * * *
    - @weekly == 0 0 * * 0
    - None == do not schedule ever, used for manually triggered DAGs
    - @once == schedule only once

## References

[Apache Airflow](https://airflow.apache.org/)

[Apache Airflow Documentation](https://airflow.apache.org/docs/apache-airflow/stable/index.html)