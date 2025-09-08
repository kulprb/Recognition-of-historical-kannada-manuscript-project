# [LS-HDIB Dataset](https://kaustubh-sadekar.github.io/LS-HDIB/)
LS-HDIB: A Large Scale Handwritten Document Image Binarization Dataset
## Setup and Install

#!/bin/bash

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Repository details
REPO_URL="https://github.com/kulprb/Recognition-of-historical-kannada-manuscript-project.git"
PROJECT_NAME="LS-HDIB"

# Function to print colored output
print_status() {
    echo -e "${GREEN}✅ $1${NC}"
}

print_error() {
    echo -e "${RED}❌ $1${NC}"
}

print_warning() {
    echo -e "${YELLOW}⚠️  $1${NC}"
}

print_info() {
    echo -e "${BLUE}📋 $1${NC}"
}

# Function to run command with error checking
run_command() {
    local cmd="$1"
    local description="$2"
    
    echo -e "${BLUE}Running: $cmd${NC}"
    if eval "$cmd"; then
        print_status "$description completed successfully"
        return 0
    else
        print_error "$description failed"
        return 1
    fi
}

# Function to check if command exists
command_exists() {
    command -v "$1" >/dev/null 2>&1
}

# Main setup function
main() {
    echo -e "${GREEN}🚀 Starting Kannada Manuscript Project Setup...${NC}"
    echo "=================================================="
    
    # Check prerequisites
    print_info "Checking prerequisites..."
    
    if ! command_exists git; then
        print_error "Git is not installed. Please install Git first."
        exit 1
    fi
    print_status "Git found"
    
    if ! command_exists python3; then
        if ! command_exists python; then
            print_error "Python is not installed. Please install Python first."
            exit 1
        else
            PYTHON_CMD="python"
        fi
    else
        PYTHON_CMD="python3"
    fi
    print_status "Python found: $PYTHON_CMD"
    
    if ! command_exists pip3; then
        if ! command_exists pip; then
            print_error "Pip is not installed. Please install pip first."
            exit 1
        else
            PIP_CMD="pip"
        fi
    else
        PIP_CMD="pip3"
    fi
    print_status "Pip found: $PIP_CMD"
    
    # Step 1: Clone repository
    echo ""
    print_info "Step 1: Cloning repository..."
    
    if [ -d "$PROJECT_NAME" ]; then
        print_warning "Directory $PROJECT_NAME already exists. Removing..."
        rm -rf "$PROJECT_NAME"
    fi
    
    if ! run_command "git clone $REPO_URL $PROJECT_NAME" "Repository cloning"; then
        print_error "Failed to clone repository. Please check your internet connection and try again."
        exit 1
    fi
    
    # Step 2: Change to project directory
    cd "$PROJECT_NAME" || {
        print_error "Failed to change to project directory"
        exit 1
    }
    
    print_status "Changed to directory: $(pwd)"
    
    # Step 3: Check for dependency files
    echo ""
    print_info "Step 2: Checking for dependency files..."
    
    DEPENDENCY_FILES=("requirements.txt" "setup.py" "environment.yml" "Pipfile" "pyproject.toml")
    FOUND_FILE=""
    
    for file in "${DEPENDENCY_FILES[@]}"; do
        if [ -f "$file" ]; then
            FOUND_FILE="$file"
            print_status "Found dependency file: $file"
            break
        fi
    done
    
    # Step 4: Install dependencies
    echo ""
    print_info "Step 3: Installing dependencies..."
    
    case "$FOUND_FILE" in
        "requirements.txt")
            run_command "$PIP_CMD install -r requirements.txt" "Requirements installation"
            ;;
        "setup.py")
            run_command "$PIP_CMD install -e ." "Setup.py installation"
            ;;
        "environment.yml")
            if command_exists conda; then
                run_command "conda env create -f environment.yml" "Conda environment creation"
            else
                print_warning "Conda not found. Trying to extract pip requirements..."
                if grep -q "pip:" environment.yml; then
                    grep -A 100 "pip:" environment.yml | grep "^[[:space:]]*-" | sed 's/^[[:space:]]*-[[:space:]]*//' > temp_requirements.txt
                    run_command "$PIP_CMD install -r temp_requirements.txt" "Extracted pip requirements installation"
                    rm -f temp_requirements.txt
                fi
            fi
            ;;
        "Pipfile")
            if command_exists pipenv; then
                run_command "pipenv install" "Pipenv installation"
            else
                print_warning "Pipenv not found. Installing pipenv first..."
                run_command "$PIP_CMD install pipenv" "Pipenv installation"
                run_command "pipenv install" "Dependencies installation"
            fi
            ;;
        *)
            print_warning "No dependency file found. Installing common ML dependencies..."
            COMMON_DEPS=(
                "numpy"
                "opencv-python"
                "tensorflow"
                "keras"
                "matplotlib"
                "pillow"
                "scikit-learn"
                "pandas"
                "scipy"
                "seaborn"
            )
            
            for dep in "${COMMON_DEPS[@]}"; do
                run_command "$PIP_CMD install $dep" "Installing $dep"
            done
            ;;
    esac
    
    # Step 5: Create verification script
    echo ""
    print_info "Step 4: Creating verification script..."
    
    cat > verify_setup.py << 'EOF'
#!/usr/bin/env python3
import sys

def check_package(package_name, import_name=None):
    if import_name is None:
        import_name = package_name
    
    try:
        __import__(import_name)
        print(f"✅ {package_name} installed")
        return True
    except ImportError:
        print(f"❌ {package_name} not found")
        return False

print("🔍 Verifying package installations...")
print("=" * 40)

packages = [
    ("NumPy", "numpy"),
    ("OpenCV", "cv2"),
    ("TensorFlow", "tensorflow"),
    ("Keras", "keras"),
    ("Matplotlib", "matplotlib"),
    ("Pillow", "PIL"),
    ("Scikit-learn", "sklearn"),
    ("Pandas", "pandas"),
    ("SciPy", "scipy"),
    ("Seaborn", "seaborn")
]

installed_count = 0
for package_name, import_name in packages:
    if check_package(package_name, import_name):
        installed_count += 1

print("=" * 40)
print(f"📊 {installed_count}/{len(packages)} packages installed successfully")

if installed_count >= len(packages) * 0.7:  # 70% threshold
    print("🎉 Setup appears to be successful!")
    sys.exit(0)
else:
    print("⚠️  Some packages are missing. You may need to install them manually.")
    sys.exit(1)
EOF
    
    # Run verification
    run_command "$PYTHON_CMD verify_setup.py" "Package verification"
    
    # Step 6: Show project structure
    echo ""
    print_info "Step 5: Project structure:"
    if command_exists tree; then
        tree -L 2
    else
        find . -maxdepth 2 -type d | head -20
        echo ""
        echo "Python files found:"
        find . -name "*.py" -maxdepth 2 | head -10
    fi
    
    # Step 7: Create quick start guide
    echo ""
    print_info "Step 6: Creating quick start guide..."
    
    cat > QUICKSTART.md << 'EOF'
# Quick Start Guide

## Project Setup Complete! 🎉

### Next Steps:
1. **Explore the project:**

## Run segmentation for your own image

```script
python run.py cpu input_2.jpg unet_best_weights.pth
```

The output file will be saved as `<INPUT_FILE_NAME>_output.jpg`. For this specific example it will be input_2_output.jpg

*For better understanding of the input arguments type `python run.py -h`*


Method 	Feature 	Classifier 	Data Set Size 	Accuracy (%) 
Govindraju et al. [18] 	Gradient 	Neural Networks 	4,506 	84 
Kompalli et al. [19] 	GSC 	Neural Network 	32,413 	84.77 
Bansal et al. [16] 	Statistical Structural 	Statistical 
Knowledge Sources 	Unspecified 	87 
Huanfeng Ma et al. [20] 	Statistical Structural 	Hausd or off image comparison 	2,727 	88.24 
Natrajan et al. [25] 	Derivatives 	HMM 	21,727 	88.24 
Bansal et al. [17] 	Filters 	Five filters 	Unspecified 	93 
Dhurandhar et al. [23] 	Contours 	Interpolation 	546 	93.03 
Kompalli et al. [22] 	GSC 	K-nearest neighbor 	9,297 	95 
Kompalli et al. [21] 	SFSA 	Stochastic finite state automa- ton 	10,606 	96 
Dhingra et al. [24] 	Gabor 	MCE 	30,000 	98.5 
Bhavesh  et al. 	Gradient 	Stochastic 
Gradient Descent 
(SGD) 	34,215 	98.64 
 
Table 7. Result comparison of OCR Systems for Printed Devanagari words 
Method 	Feature 	Classifier 	Data Set Size 	Accura cy (%) 
Govindraju et al. [18] 	Gradient 	Neural Networks 	4,506 	53 
Kompalli et al. [22] 	GSC 	K-nearest neighbor 	1,882 	58.51 
Kompalli et al. [19] 	GSC 	Neural Network 	14,353 	61.8 
Ma et al. [20] 	Statistical Structural 	Hausdorff image comparison 	2,727 	66.78 
Kompalli et al. [21] 	SFSA 	Stochastic finite state automaton 	10,606 	87 
Bhavesh  et al. 	Gradient 	Stochastic Gradient Descent (SGD) 	15,455 	97.3 
 
