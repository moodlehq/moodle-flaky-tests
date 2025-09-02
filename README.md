# moodle-test-skipper

This project provides a set of scripts to automatically skip known flaky tests for both Behat scenarios and PHPUnit tests in a Moodle environment. This is useful for avoiding the execution of these tests during Continuous Integration (CI) runs, ensuring more stable and reliable build results.

### Purpose

The `moodle-test-skipper` tool is designed to:
- Read lists of flaky Behat scenarios and PHPUnit tests from dedicated files.
- Modify Behat test files by adding an `@skip` tag above the flaky scenarios.
- Modify PHPUnit test files by injecting the `$this->markTestSkipped(...)` method call at the beginning of flaky test functions.
- Help streamline CI processes by programmatically skipping tests that are known to fail intermittently.

### File Structure

The tool is organized with an orchestrator script (`skip_tests`) that calls specialist scripts for each test type. The flaky test lists are kept in a dedicated directory.
```
skipper_tools/
    ├── skip_tests                  # Main orchestrator script for CI
    ├── inject_behat_skips          # Specialist script for Behat
    ├── inject_phpunit_skips        # Specialist script for PHPUnit
    └── flaky_tests/
        ├── 501_firefox_flaky_tests.txt
        ├── 501_phpunit_flaky_tests.txt
        └── ...
```

### Usage

The main entry point is the `skip_tests` orchestrator script, which requires the test type as the first parameter (`behat` or `phpunit`).

#### Skipping Behat Tests

- **`<branch>`**: The branch name, used to find the flaky scenarios file.
- **`<browser>`**: The browser name, used to find the flaky scenarios file.
- **`<path/to/moodle/root>`**: The absolute path to the Moodle root directory.

```
./skipper_tools/skip_tests behat <branch> <browser> <path/to/moodle/root>
```

#### Skipping PHPUnit Tests

- **`<branch>`**: The branch name, used to find the flaky test file.
- **`<path/to/moodle/root>`**: The absolute path to the Moodle root directory.

```
./skipper_tools/skip_tests phpunit <branch> <path/to/moodle/root>
```

### Flaky Test Lists

The scripts expect flaky test lists to be located in the `flaky_tests/` directory.

#### Behat Scenarios (`..._flaky_tests.txt`)

The script expects a file named `<branch>_<browser>_flaky_tests.txt`. Each line in the file should follow this format:

`scenario_name|relative_file_path`

- `scenario_name`: The name of the scenario to skip.
- `relative_file_path`: The path to the `.feature` file, relative to the Moodle root.

**Example:** `501_firefox_flaky_tests.txt`
```
Admins can replace the original .h5p file with a new one|public/contentbank/contenttype/h5p/tests/behat/admin_replace_content.feature
Check that a region with only hidden blocks is not docked|public/blocks/tests/behat/hidden_block_region.feature
```

#### PHPUnit Tests (`..._phpunit_flaky_tests.txt`)

The script expects a file named `<branch>_phpunit_flaky_tests.txt`. Each line in the file should follow this format:

`test_method_name|relative_file_path`

- `test_method_name`: The name of the test function to skip (e.g., `test_user_can_login`).
- `relative_file_path`: The path to the `_test.php` file, relative to the Moodle root.

**Example:** `501_phpunit_flaky_tests.txt`
```
test_notification_races_on_send|message/tests/output/notification_test.php
test_assignment_submission_event|mod/assign/tests/event_test.php
```

### Output

- The scripts will modify the specified test files in place.
- For Behat, an `@skip` tag is added.
- For PHPUnit, `$this->markTestSkipped(...)` is injected.
- The console will show logs indicating success, warnings for missing tests, or errors for missing files.

## Notes

- Ensure the scripts have execute permissions: `chmod +x skipper_tools/*`.
- The scripts modify test files directly. It is recommended to run this tool in a CI environment where changes are ephemeral. Use version control to track any intended changes.
