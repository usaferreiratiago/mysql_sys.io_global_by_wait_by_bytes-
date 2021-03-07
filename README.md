# mysql_sys.io_global_by_wait_by_bytes-

SELECT 
    SUBSTRING_INDEX(`performance_schema`.`file_summary_by_event_name`.`EVENT_NAME`,
            '/',
            -(2)) AS `event_name`,
    `performance_schema`.`file_summary_by_event_name`.`COUNT_STAR` AS `total`,
    FORMAT_PICO_TIME(`performance_schema`.`file_summary_by_event_name`.`SUM_TIMER_WAIT`) AS `total_latency`,
    FORMAT_PICO_TIME(`performance_schema`.`file_summary_by_event_name`.`MIN_TIMER_WAIT`) AS `min_latency`,
    FORMAT_PICO_TIME(`performance_schema`.`file_summary_by_event_name`.`AVG_TIMER_WAIT`) AS `avg_latency`,
    FORMAT_PICO_TIME(`performance_schema`.`file_summary_by_event_name`.`MAX_TIMER_WAIT`) AS `max_latency`,
    `performance_schema`.`file_summary_by_event_name`.`COUNT_READ` AS `count_read`,
    FORMAT_BYTES(`performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_READ`) AS `total_read`,
    FORMAT_BYTES(IFNULL((`performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_READ` / NULLIF(`performance_schema`.`file_summary_by_event_name`.`COUNT_READ`,
                            0)),
                    0)) AS `avg_read`,
    `performance_schema`.`file_summary_by_event_name`.`COUNT_WRITE` AS `count_write`,
    FORMAT_BYTES(`performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_WRITE`) AS `total_written`,
    FORMAT_BYTES(IFNULL((`performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_WRITE` / NULLIF(`performance_schema`.`file_summary_by_event_name`.`COUNT_WRITE`,
                            0)),
                    0)) AS `avg_written`,
    FORMAT_BYTES((`performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_WRITE` + `performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_READ`)) AS `total_requested`
FROM
    `performance_schema`.`file_summary_by_event_name`
WHERE
    ((`performance_schema`.`file_summary_by_event_name`.`EVENT_NAME` LIKE 'wait/io/file/%')
        AND (`performance_schema`.`file_summary_by_event_name`.`COUNT_STAR` > 0))
ORDER BY (`performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_WRITE` + `performance_schema`.`file_summary_by_event_name`.`SUM_NUMBER_OF_BYTES_READ`) DESC
