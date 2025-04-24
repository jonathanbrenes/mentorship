# Python
## Enhanced Performance in Python 3.11 vs 3.6
### Create a SLES 15 server which it will have python 3.6 and 3.11 
Deploy this VM. This will automatically create a VM.
  
  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjonathanbrenes%2Fmentorship%2Frefs%2Fheads%2Fmain%2Fpython001.json)
  
- Demonstrating Enhanced Performance in Python 3.11 vs 3.6 Using an N-body Simulation Script
  
  This will showcase the improved performance of Python 3.11 over Python 3.6 by running an extended N-body simulation. The script calculates the gravitational interactions between celestial bodies, providing a computationally intensive benchmark to highlight the efficiency gains in Python 3.11.
- Execute the python script using different versions
  Use python 3.6 and 3.11 available on SLES 15

  ```bash
  python3.6 /usr/local/bin/nbody.py
  python3.11 /usr/local/bin/nbody.py
  ```
 > **Note:** Result of the script will be faster with 3.11. That's one of reasons everybody is moving to newest python version
