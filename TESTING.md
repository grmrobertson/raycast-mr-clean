# Testing Plan for Mr. Clean Changes

## Overview
This document outlines the testing procedures for the new features added to Mr. Clean:
1. Configurable timestamp feature (`USE_CHANGE_TIME`)
2. Default directory change to `~/Downloads`
3. Bug fixes for variable name issues

## Test Environment Setup

### Prerequisites
- macOS system
- Bash shell
- Test files with different creation and modification dates
- Clean test directories

### Test Directory Structure
```
~/test-mr-clean/
├── test-downloads/          # Simulates Downloads folder
├── test-documents/          # Alternative test location
└── test-files/             # Files with known timestamps
    ├── old-file.txt        # Created weeks ago
    ├── new-file.txt        # Created today
    └── extracted-folder/   # Folder with preserved timestamps
```

## Test Cases

### 1. Default Directory Testing

#### Test 1.1: Default to Downloads
**Purpose**: Verify script defaults to `~/Downloads` when no directory specified

**Steps**:
1. Run script without directory parameter
2. Verify it uses `~/Downloads` as target
3. Check output message confirms default directory

**Expected Result**:
```
No directory provided, using default directory: /Users/[username]/Downloads
```

#### Test 1.2: Custom Directory Override
**Purpose**: Verify custom directory still works when specified

**Steps**:
1. Run script with custom directory parameter
2. Verify it uses the specified directory
3. Confirm no default directory message appears

**Expected Result**:
- Script operates on specified directory
- No default directory message shown

### 2. Configurable Timestamp Testing

#### Test 2.1: Birth Time (Default Behavior)
**Purpose**: Verify original behavior with `USE_CHANGE_TIME="false"`

**Setup**:
```bash
export USE_CHANGE_TIME="false"
```

**Steps**:
1. Create test files with known birth times
2. Run Mr. Clean on test directory
3. Verify files are organized by birth time (creation date)

**Expected Result**:
- Files organized by original creation date
- Extracted archives maintain original timestamps

#### Test 2.2: Change Time (New Feature)
**Purpose**: Verify new behavior with `USE_CHANGE_TIME="true"`

**Setup**:
```bash
export USE_CHANGE_TIME="true"
```

**Steps**:
1. Use same test files as Test 2.1
2. Run Mr. Clean on test directory
3. Verify files are organized by change time (download date)

**Expected Result**:
- Files organized by when they were copied/downloaded
- Recent downloads appear in current month folders

#### Test 2.3: Configuration Toggle
**Purpose**: Verify configuration can be changed between runs

**Steps**:
1. Run with `USE_CHANGE_TIME="false"` 
2. Note file organization
3. Undo the clean
4. Change to `USE_CHANGE_TIME="true"`
5. Run again on same files
6. Compare organization differences

**Expected Result**:
- Different organization patterns between runs
- Configuration change takes effect immediately

### 3. Bug Fix Testing

#### Test 3.1: Calling Card Creation
**Purpose**: Verify `mr_clean_was_here.txt` is created properly

**Steps**:
1. Run Mr. Clean on test directory
2. Check for `mr_clean_was_here.txt` in target directory
3. Verify file contains timestamp

**Expected Result**:
- File exists in target directory
- Contains "Last visited by Mr. Clean on [date]"

#### Test 3.2: Calling Card Cleanup (Undo)
**Purpose**: Verify calling card is removed during undo

**Steps**:
1. Run Mr. Clean (creates calling card)
2. Run undo operation
3. Verify calling card is removed

**Expected Result**:
- `mr_clean_was_here.txt` is deleted during undo
- No error messages about missing file

### 4. Edge Cases

#### Test 4.1: Empty Directory
**Purpose**: Verify script handles empty directories gracefully

**Steps**:
1. Create empty test directory
2. Run Mr. Clean
3. Verify no errors occur

**Expected Result**:
- Script completes without errors
- Calling card is still created

#### Test 4.2: Mixed File Types
**Purpose**: Verify script handles files and folders correctly

**Steps**:
1. Create directory with mixed content:
   - Regular files
   - Folders
   - Hidden files
   - Symlinks
2. Run Mr. Clean
3. Verify all items are processed appropriately

**Expected Result**:
- All items organized by configured timestamp
- No items lost or corrupted

#### Test 4.3: Permission Issues
**Purpose**: Verify script handles permission errors gracefully

**Steps**:
1. Create files with restricted permissions
2. Run Mr. Clean
3. Check for appropriate error handling

**Expected Result**:
- Clear error messages for permission issues
- Script doesn't crash or corrupt data

### 5. Archive Function Testing

#### Test 5.1: Archive Old Folders
**Purpose**: Verify folders older than 6 months are archived

**Steps**:
1. Create folders with timestamps older than 6 months
2. Run Mr. Clean
3. Verify old folders moved to `_archive`

**Expected Result**:
- Old folders in `_archive` directory
- Recent folders remain in main directory

#### Test 5.2: Archive Undo
**Purpose**: Verify archive can be undone

**Steps**:
1. Run Mr. Clean (creates archive)
2. Run undo operation
3. Verify archived folders are restored

**Expected Result**:
- Archived folders moved back to main directory
- `_archive` directory is removed

### 6. Integration Testing

#### Test 6.1: Full Workflow
**Purpose**: Test complete organize → archive → undo cycle

**Steps**:
1. Create test directory with varied file ages
2. Run Mr. Clean with both timestamp configurations
3. Verify organization and archiving
4. Run undo operation
5. Verify complete restoration

**Expected Result**:
- Files properly organized and archived
- Complete restoration to original state

## Test Data Generation

### Creating Test Files with Specific Timestamps

```bash
# Create files with specific birth times
touch -t 202501010000 old-file.txt
touch -t 202507090000 new-file.txt

# Create files with specific modification times
touch -m -t 202505010000 downloaded-file.txt
```

### Verifying Timestamps

```bash
# Check birth time
stat -f "Birth: %SB" filename

# Check modification time  
stat -f "Modify: %Sm" filename

# Check change time
stat -f "Change: %Sc" filename
```

## Test Execution Checklist

- [ ] Set up test environment
- [ ] Create test files with known timestamps
- [ ] Test default directory behavior
- [ ] Test both timestamp configurations
- [ ] Test bug fixes (calling card)
- [ ] Test edge cases
- [ ] Test archive functionality
- [ ] Test undo functionality
- [ ] Clean up test environment

## Test Results Documentation

For each test case, document:
- ✅ **PASS** / ❌ **FAIL** status
- Actual vs expected results
- Any issues or unexpected behavior
- Screenshots or logs if applicable

## Cleanup

After testing, clean up test directories:
```bash
rm -rf ~/test-mr-clean
```
