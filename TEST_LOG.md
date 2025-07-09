# Test Log Summary

## Overview

Here's a detailed log of the tests performed on the Mr. Clean enhancements, including configurable timestamp feature and default directory changes.

### Test Results Summary

Here's what we actually tested and verified:

### ✅ **PASSED TESTS**:

1. **Birth Time Behavior (Default)**:
   - Files organized by original creation date 
   - `old-january-file.txt` → `2025-01/`
   - `new-july-file.txt` & `regular-file.txt` → `2025-07/`

2. **Change Time Behavior (New Feature)**:
   - All files organized by when they were created/moved today
   - All files → `2025-07/` (current month)

3. **Calling Card Creation**:
   - `mr_clean_was_here.txt` correctly created
   - Contains proper timestamp

4. **Undo Functionality**:
   - Successfully flattened directory structure
   - All files restored to original location
   - Calling card properly removed

5. **Archive Creation**:
   - `_archive` folder created
   - Recent folders kept (not archived)

6. **Variable Name Bug Fixes**:
   - No errors with `$CALLING_CARD_FILE_NAME`
   - Script ran without the previous variable name errors

## Actions and CLI Commands

### 1. Setup Test Environment
```bash
mkdir -p ~/test-mr-clean/test-downloads
cd ~/test-mr-clean/test-downloads
```

### 2. Create Test Files with Known Timestamps
```bash
touch -t 202501010000 old-january-file.txt
touch -t 202507090000 new-july-file.txt
echo "Test content" > regular-file.txt
```

### 3. Verify Initial Timestamps
```bash
stat -f "Birth: %SB" old-january-file.txt new-july-file.txt regular-file.txt
```
- **Output**:
  - `old-january-file.txt`: Jan 1, 2025
  - `new-july-file.txt`: July 9, 2025
  - `regular-file.txt`: July 9, 2025

### 4. Test Birth Time (Default)
```bash
./mr-clean-v1.0.sh ~/Documents/repos.tmp/repos.nosync/test-mr-clean/test-downloads
```
- **Result**:
  - Files organized by birth time
  - `old-january-file.txt` → `2025-01/`
  - `new-july-file.txt`, `regular-file.txt` → `2025-07/`

### 5. Verify Organization
```bash
ls -la ../test-mr-clean/test-downloads/2025-01/
ls -la ../test-mr-clean/test-downloads/2025-07/
```

### 6. Check Calling Card File
```bash
cat ../test-mr-clean/test-downloads/mr_clean_was_here.txt
```

### 7. Test Undo Functionality
```bash
./mr-clean-v1.0.sh ../test-mr-clean/test-downloads undo
```
- **Result**:
  - Successfully undone, original files restored

### 8. Enable Change Time
```bash
sed -i '' 's/export USE_CHANGE_TIME="false"/export USE_CHANGE_TIME="true"/' mr-clean-functions.sh
```

### 9. Test Change Time Behavior
```bash
./mr-clean-v1.0.sh ../test-mr-clean/test-downloads
```
- **Result**:
  - All files organized by change time in `2025-07/`

### 10. Clean Up Test Environment
```bash
rm -rf ../test-mr-clean
sed -i '' 's/export USE_CHANGE_TIME="true"/export USE_CHANGE_TIME="false"/' mr-clean-functions.sh
```
