# pytest.ini

```ini
[pytest]


# Custom Marker
markers =
    p0
    p1
    p2
    p3

    ui
    api
    bvt
    function
    security
    stress

    monitor


# Disable Warnings
addopts = -p no:warnings --alluredir=allure-result --clean-alluredir --strict


# Log Setting => DEBUG < INFO < WARNING < ERROR < CRITICAL
log_cli = 1
log_cli_level = INFO
log_cli_format = %(asctime)s [%(levelname)s] %(message)s
log_cli_date_format = %Y-%m-%d %H:%M:%S

log_file = pytest.log
log_file_level = INFO
log_file_format = %(asctime)s [%(levelname)s] %(message)s
log_file_date_format = %Y-%m-%d %H:%M:%S


# Ignore Warning
filterwarnings = ignore::PendingDeprecationWarning


# Chinese Support
disable_test_id_escaping_and_forfeit_all_rights_to_community_support = True
```