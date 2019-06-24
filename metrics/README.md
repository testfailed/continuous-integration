# Bazel CI Metrics Service

## SQL Setup

Run the following commands to initialize an empty database `metrics`:

NOTE: Double check that the following commands match the output of `git grep "CREATE TABLE"`.

```sql
USE metrics;

CREATE TABLE build_success (org VARCHAR(255), pipeline VARCHAR(255), build INT, linux VARCHAR(255), macos VARCHAR(255), windows VARCHAR(255), rbe VARCHAR(255), PRIMARY KEY(org, pipeline, build));
CREATE TABLE builds_per_change (org VARCHAR(255), pipeline VARCHAR(255), changelist INT, builds INT, PRIMARY KEY(org, pipeline, changelist));
CREATE TABLE critical_path (org VARCHAR(255), pipeline VARCHAR(255), build INT, wait_time_seconds FLOAT, run_time_seconds FLOAT, longest_task_name VARCHAR(255), longest_task_time_seconds FLOAT, result VARCHAR(255), PRIMARY KEY(org, pipeline, build));
CREATE TABLE flakiness (org VARCHAR(255), pipeline VARCHAR(255), build INT, target VARCHAR(255), passed_count INT, failed_count INT, PRIMARY KEY(org, pipeline, build, target));
CREATE TABLE mac_performance (org VARCHAR(255), pipeline VARCHAR(255), build INT, wait_time_seconds FLOAT, run_time_seconds FLOAT, skipped BOOL, PRIMARY KEY(org, pipeline, build));
CREATE TABLE pipeline_performance (org VARCHAR(255), pipeline VARCHAR(255), build INT, job VARCHAR(255), creation_time DATETIME, wait_time_seconds FLOAT, run_time_seconds FLOAT, skipped_tasks VARCHAR(255), PRIMARY KEY(org, pipeline, build, job));
CREATE TABLE platform_load (timestamp DATETIME, org VARCHAR(255), platform VARCHAR(255), waiting_jobs INT, running_jobs INT, PRIMARY KEY(org, timestamp, platform));
CREATE TABLE platform_significance (org VARCHAR(255), pipeline VARCHAR(255), total_builds INT, passing_builds INT, canceled_builds INT, setup_failed INT, linux_failures INT, macos_failures INT, windows_failures INT, rbe_failures INT, multi_platform_failures INT, PRIMARY KEY(org, pipeline));
CREATE TABLE platform_usage (org VARCHAR(255), pipeline VARCHAR(255), build INT, platform VARCHAR(255), usage_seconds FLOAT, PRIMARY KEY(org, pipeline, build, platform));
CREATE TABLE release_downloads (release_name VARCHAR(255), artifact VARCHAR(255), downloads INT, PRIMARY KEY(release_name, artifact));
CREATE TABLE worker_availability (timestamp BIGINT, org VARCHAR(255), platform VARCHAR(255), idle_count INT, busy_count INT, PRIMARY KEY(timestamp, org, platform));
```

## Service Deployment

- `gcloud app deploy metrics/app.yaml --stop-previous-version`
- `gcloud app logs tail -s default`