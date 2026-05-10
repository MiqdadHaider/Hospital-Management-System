# MediCore — Hospital Management System

A C++17 hospital management system with two interfaces: a console-based CLI (`Main.cpp`) and a graphical GUI built with SFML 2.5 (`GUI_main.cpp`). All source files live in the **same directory**. Because both interfaces share the same project, only one can be built at a time — you switch between them by excluding the unused entry point from the build.

---

## Project Structure

```
project/
├── data/                      # Auto-created at runtime (CSV data files)
├── CMakeLists.txt             # Used for building the GUI with CMake
├── GUI.h
├── GUI_main.cpp               # GUI entry point  (contains main())
├── Gui_app.cpp
├── Gui_login_patient.cpp
├── Gui_doctor.cpp
├── Gui_admin.cpp
├── Gui_widgets.cpp
├── Main.cpp                   # CLI entry point  (contains main())
├── person.h / person.cpp
├── patient.h / patient.cpp
├── doctor.h / doctor.cpp
├── admin.h / admin.cpp
├── appointment.h / Appointment.cpp
├── bill.h / Bill.cpp
├── prescription.h / prescription.cpp
├── storage.h
├── filehandler.h / fileHandler.cpp
├── validator.h / validator.cpp
├── hospitalException.h / hospitalException.cpp
├── patientSession.h / patientSession.cpp
├── doctorSession.h / doctorSession.cpp
└── adminSession.h / AdminSession.cpp
```

---

## Prerequisites

### CLI version
- **Visual Studio 2019+** (any edition) with the *Desktop development with C++* workload, **or**
- **g++ 9+** / **Clang 9+** on Linux or macOS

### GUI version
- Everything above, plus:
- **SFML 2.5** (graphics, window, system components)
- **CMake 3.16+** (if building outside Visual Studio)
- A system font at one of these paths (checked automatically at runtime):
  - `C:/Windows/Fonts/arial.ttf` (Windows — usually present by default)
  - `/Library/Fonts/Arial.ttf` (macOS)
  - `resources/font.ttf` (place any `.ttf` here as a fallback)

---

## Building in Visual Studio

Both `Main.cpp` and `GUI_main.cpp` define `main()`, so exactly one must be **excluded from the build** at all times.

### To build the GUI

1. In **Solution Explorer**, right-click `Main.cpp` → **Properties**.
2. Under **Configuration Properties → General**, set **Excluded From Build** to **Yes**. Click OK.
3. Make sure `GUI_main.cpp` (and all other `Gui_*.cpp` files) are **not** excluded.
4. Add SFML to the project:
   - **C/C++ → Additional Include Directories**: path to `SFML/include`
   - **Linker → Additional Library Directories**: path to `SFML/lib`
   - **Linker → Additional Dependencies**: `sfml-graphics.lib;sfml-window.lib;sfml-system.lib` (Debug builds: `sfml-graphics-d.lib` etc.)
5. Copy the SFML `.dll` files into the project output folder (same folder as the `.exe`).
6. Build and run (`Ctrl+F5`).

### To build the CLI

1. In **Solution Explorer**, right-click `GUI_main.cpp` → **Properties**.
2. Set **Excluded From Build** to **Yes** for `GUI_main.cpp` and all `Gui_*.cpp` files. Click OK.
3. Make sure `Main.cpp` is **not** excluded.
4. Remove or disable the SFML linker dependencies (they are not needed for the CLI).
5. Build and run (`Ctrl+F5`).

---

## Building with CMake (GUI only — Linux / macOS / Windows)

### Step 1 — Install SFML 2.5

**Ubuntu / Debian**
```bash
sudo apt install libsfml-dev
```

**macOS (Homebrew)**
```bash
brew install sfml
```

**Windows**
Download SFML 2.5 from https://www.sfml-dev.org/download.php and extract it somewhere (e.g. `C:/SFML-2.5.1`).

### Step 2 — Build

Run from the project directory (where `CMakeLists.txt` lives):

```bash
mkdir build
cd build
cmake ..
cmake --build .
```

On Windows, if CMake cannot locate SFML automatically:

```bash
cmake .. -DSFML_DIR="C:/SFML-2.5.1/lib/cmake/SFML"
```

### Step 3 — Run

**Linux / macOS** (run from the project root so `data/` is found):
```bash
./build/MediCoreGUI
```

**Windows** (CMake copies `data/` next to the `.exe` automatically):
```bat
build\MediCoreGUI.exe
```

---

## Building the CLI with g++ (Linux / macOS)

```bash
g++ -std=c++17 -o medicore \
    Main.cpp person.cpp patient.cpp doctor.cpp admin.cpp \
    Appointment.cpp Bill.cpp prescription.cpp \
    validator.cpp hospitalException.cpp fileHandler.cpp \
    patientSession.cpp doctorSession.cpp AdminSession.cpp

./medicore
```

---

## Default Login

On first run, if `data/admin.txt` is empty, a default admin account is created automatically:

| Role  | ID | Password  |
|-------|----|-----------|
| Admin | 1  | admin123  |

Log in as Admin first to add doctors and patients before using those roles.

---

## Data Files

All data is stored as plain CSV in the `data/` directory, which is created automatically on first run:

| File                     | Contents                      |
|--------------------------|-------------------------------|
| `data/patients.txt`      | Patient records               |
| `data/doctors.txt`       | Doctor records                |
| `data/admin.txt`         | Admin credentials             |
| `data/appointments.txt`  | Appointment records           |
| `data/bills.txt`         | Billing records               |
| `data/prescriptions.txt` | Prescription records          |
| `data/security_log.txt`  | Login attempt audit log       |
| `data/discharged.txt`    | Archived discharged patients  |

> The `data/` folder must be in the **working directory** when the program runs. In Visual Studio this is the project directory by default. If you move the executable, copy `data/` alongside it.

---

## Features

**Patient**
- Book appointments by specialization search
- Cancel appointments (full fee refunded)
- View appointments, medical records, and bills
- Pay bills and top up account balance

**Doctor**
- View today's appointments sorted by time slot
- Mark appointments as completed or no-show
- Write prescriptions for completed appointments
- View medical history for own patients only

**Admin**
- Add / remove doctors and patients
- View all patients, doctors, and appointments
- View unpaid bills with overdue flagging (7+ days)
- Discharge patients (archives all records)
- View security log and generate daily reports

---

## Notes

- The storage limit is **100 records per entity type**. To increase it, change the array sizes in `storage.h` and in the `AppointmentStore`, `BillStore`, and `PrescriptionStore` structs in `filehandler.h`.
- Passwords are stored in plain text — this project is intended for educational use.
- Do not delete or manually edit files inside `data/` while the application is running.
