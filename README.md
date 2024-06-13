import subprocess

def run_kinit(password, principal):
    try:
        # Create a subprocess to run the kinit command
        kinit_process = subprocess.Popen(['kinit', principal], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)

        # Pass the password to the kinit command
        stdout, stderr = kinit_process.communicate(input=password.encode())

        # Check if kinit was successful
        if kinit_process.returncode == 0:
            print("Kinit successful")
        else:
            print("Kinit failed with error:", stderr.decode())

    except Exception as e:
        print("Error:", e)

# Replace 'YourKerberosPassword' and 'YourKerberosPrincipal@YOUR.REALM' with your actual values
password = 'YourKerberosPassword'
principal = 'YourKerberosPrincipal@YOUR.REALM'

run_kinit(password, principal)
