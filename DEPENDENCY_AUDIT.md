# Dependency Audit Report
**Project:** 2025 F1 Predictions
**Date:** 2025-12-18
**Python Version:** 3.11.14

---

## Executive Summary

This audit analyzed the project's dependencies for security vulnerabilities, outdated packages, and unnecessary bloat. The project currently **lacks a requirements.txt file**, making dependency management inconsistent and deployment unreliable.

### Key Findings:
- ✅ **No security vulnerabilities** found in latest package versions
- ✅ **All packages are up-to-date** (latest stable versions)
- ⚠️ **One unnecessary dependency identified** (numpy - imported but unused)
- ❌ **No requirements.txt file** exists

---

## Current Dependencies Analysis

### Required Dependencies

| Package | Latest Version | Status | Purpose | Necessity |
|---------|---------------|--------|---------|-----------|
| **fastf1** | 3.7.0 | ✅ Current | Fetch F1 race and session data | **Essential** |
| **pandas** | 2.3.3 | ✅ Current | Data manipulation and analysis | **Essential** |
| **scikit-learn** | 1.8.0 | ✅ Current | Machine learning models (GradientBoostingRegressor) | **Essential** |
| **numpy** | 2.3.5 | ⚠️ **UNUSED** | Numerical computing (not used in code) | **Remove** |

### Dependency Details

#### 1. fastf1 (v3.7.0)
- **Usage:** All prediction files use `fastf1.get_session()` and `fastf1.Cache`
- **Files:** prediction1.py:2, prediction2.py:1, prediction2_nochange.py:1, prediction2_olddrivers.py:1
- **Security:** ✅ No known vulnerabilities
- **Recommendation:** **Keep** - Core functionality

#### 2. pandas (v2.3.3)
- **Usage:** DataFrame operations, data merging, grouping, sorting
- **Files:** All prediction files (lines 3, 2, 2, 2 respectively)
- **Security:** ✅ No known vulnerabilities
- **Recommendation:** **Keep** - Essential for data manipulation

#### 3. scikit-learn (v1.8.0)
- **Usage:**
  - `GradientBoostingRegressor` - ML model for predictions
  - `train_test_split` - Dataset splitting
  - `mean_absolute_error` - Model evaluation
- **Files:** All prediction files (lines 5-7, 4-6, 4-6, 4-6)
- **Security:** ✅ No known vulnerabilities
- **Recommendation:** **Keep** - Core ML functionality

#### 4. numpy (v2.3.5) ⚠️
- **Usage:** **NONE** - Imported via `import numpy as np` but never used
- **Files:** All prediction files import it but no `np.` calls found
- **Security:** ✅ No vulnerabilities, but still unnecessary
- **Recommendation:** **REMOVE** - Unnecessary bloat (adds ~50MB to installation)

---

## Security Audit Results

### Vulnerability Scan
```
✅ No known vulnerabilities found
```

Tested using `pip-audit` against:
- fastf1==3.7.0
- pandas==2.3.3
- numpy==2.3.5
- scikit-learn==1.8.0

All packages passed security checks against the OSV (Open Source Vulnerabilities) database.

---

## Recommendations

### 1. IMMEDIATE ACTIONS

#### Create requirements.txt
**Priority:** HIGH
**Impact:** Ensures reproducible builds and consistent environments

Create a `requirements.txt` file with pinned versions:

```txt
fastf1==3.7.0
pandas==2.3.3
scikit-learn==1.8.0
```

**Note:** numpy excluded as it's unused.

#### Remove numpy imports
**Priority:** MEDIUM
**Impact:** Reduces installation size (~50MB) and dependency tree

Remove the following lines from all Python files:
- `prediction1.py:4`
- `prediction2.py:3`
- `prediction2_nochange.py:3`
- `prediction2_olddrivers.py:3`

### 2. OPTIONAL IMPROVEMENTS

#### Add development dependencies
Consider creating `requirements-dev.txt` for development tools:

```txt
# Testing
pytest==8.3.4
pytest-cov==6.0.0

# Code quality
black==24.12.0
flake8==7.1.2
mypy==1.15.0

# Security scanning
pip-audit==2.10.0
```

#### Pin transitive dependencies
For maximum reproducibility, consider using `pip freeze` to lock all transitive dependencies:

```bash
pip install -r requirements.txt
pip freeze > requirements.lock
```

#### Add .gitignore
Create `.gitignore` to exclude cache directories:

```
f1_cache/
__pycache__/
*.pyc
*.pyo
.pytest_cache/
venv/
.env
```

### 3. MAINTENANCE SCHEDULE

Recommend quarterly dependency updates:

```bash
# Check for outdated packages
pip list --outdated

# Check for security vulnerabilities
pip-audit

# Update requirements.txt accordingly
```

---

## Dependency Tree Analysis

### Direct Dependencies (Recommended)
```
fastf1==3.7.0
├── requests (HTTP client for API calls)
├── pandas (already included)
└── numpy (transitive - pandas requires it)

pandas==2.3.3
├── numpy>=1.26.0 (already satisfied)
├── python-dateutil
└── pytz

scikit-learn==1.8.0
├── numpy>=1.25.0 (already satisfied)
├── scipy
└── joblib
```

**Important Note:** Even though we're removing the direct `numpy` import from the code, numpy will still be installed as a transitive dependency of pandas and scikit-learn. This is fine - we're just removing the unused direct dependency.

---

## Testing Recommendations

Before deploying changes, test the environment:

```bash
# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Test each prediction script
python prediction1.py
python prediction2.py
python prediction2_nochange.py
python prediction2_olddrivers.py
```

---

## Conclusion

The project's dependencies are in good shape with **no security vulnerabilities** and all packages at their latest stable versions. The main issues are:

1. **Missing requirements.txt** - Critical for deployment
2. **Unused numpy import** - Minor bloat that should be removed

Implementing the recommended changes will result in a cleaner, more maintainable, and properly documented project.
