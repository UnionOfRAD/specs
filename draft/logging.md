## Abstract

The logger facilitates logging messages to an adapter. A typical logging adapter would be a file or syslog, of course this can be expanded to support other logging systems very easily.

## Log and Error Levels

Log levels are used to determine the severity of a log entry

The available log levels are **debug**, **info**, **notice**, **warning**, **error** and **critical**. These are the most common and widely used log levels.

Of these log levels, **notice**, **warning** and **error** are also used by the error handler to log PHP errors. These levels very closely correlate with PHP error levels.

