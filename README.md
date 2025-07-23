# moodle-skip-tag-injector

This project provides a script to inject the `@skip` tag into specified flaky scenarios in Moodle Behat test files. 
This is useful for avoiding the execution of known flaky tests during Continuous Integration (CI) runs, ensuring more stable and reliable test results.

### Purpose

The `inject_skip_tag` script is designed to:
- Read a list of flaky scenarios and their corresponding file paths.
- Modify the specified Behat test files by adding the `@skip` tag above the flaky scenarios.
- Help streamline CI processes by skipping tests that are known to fail intermittently.

### Prerequisites

- A file listing the flaky scenarios in the format: `<relative_file_path>|<scenario_name>`.
- Access to the Moodle root directory where the Behat test files are located.

### Usage

Run the script with the following parameters:
### Parameters

- `<branch>`: The branch name (used to locate the flaky scenarios file).
- `<browser>`: The browser name (used to locate the flaky scenarios file).
- `<path/to/moodle/root>`: The absolute path to the Moodle root directory.

```
./inject_skip_tag <branch> <browser> <path/to/moodle/root>
```
### Flaky Scenarios File

The script expects a file named `<branch>_<browser>_flaky_tests.txt` in the same directory as the script. Each line in the file should follow this format:

```
<relative_file_path>|<scenario_name>
```

- `relative_file_path`: The path to the Behat test file, relative to the Moodle root directory.
- `scenario_name`: The name of the scenario to skip.

### Output

- The script will inject the `@skip` tag above the specified scenarios in the test files.
- It will log messages indicating success, warnings for missing scenarios, or errors for missing files.

## Notes

- Ensure the script has execute permissions: `chmod +x inject_skip_tag`.
- The script modifies test files in place. Use version control to track changes.
