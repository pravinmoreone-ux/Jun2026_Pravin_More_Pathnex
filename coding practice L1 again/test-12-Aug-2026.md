from pathlib import Path

file = Path("/tmp/app.log")

if file.exists():
    print("File exists")
else:
    print("File does not exist")

>>>>>>>>>>>>>>>>

from pathlib import Path

file = Path("/tmp/app/log")

if file.exists():
    print("File exists")
else:
    print("File does not exist")


>>>>>>>>>>>>>>>>>>>>>>>>>>


Read a log file and find ERROR lines

with open("app.log", "r") as file:
    for line in file:
        if "ERROR" in line:
            print(line.strip())

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


Count ERROR and WARNING messages


error_count = 0
warning_count = 0

with open("app.log") as file:
    for line in file:
        if "ERROR" in line:
            error_count += 1
        elif "WARNING" in line:
            warning_count += 1


print("Errors:", error_count)
print("Warnings:", warning_count)


>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


import subprocess

result = subprocess.run(
    ["kubectl", "get", "pods"],
    capture_output=True,
    text=True
)

print(result.stdout)
print(result.stderr)

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>


