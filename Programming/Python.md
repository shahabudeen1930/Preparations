#### Python Script to Collect details of AWS Instances
```
import boto3 
import os as ios
from tabulate import tabulate

# This script fetches and displays information about running EC2 instances in a specified AWS region.
# Function to fetch running EC2 instances
def get_ec2_instances(region_name='reg-ap-south'):
    """
    Fetch running EC2 instances with their IP addresses, state, and key pair.
    
    Args:
        region_name (str): AWS region to query. Defaults to 'us-east-1'.
    
    Returns:
        list: List of dictionaries containing instance information.
    """
    # Create EC2 client
    ec2 = boto3.client('ec2', region_name=region_name)
    
    # Describe instances with 'running' state
    response = ec2.describe_instances(
        Filters=[{'Name': 'instance-state-name', 'Values': ['running']}]
    )
    
    instances_info = []
    
    for reservation in response['Reservations']:
        for instance in reservation['Instances']:
            # Get public IP (if available)
            public_ip = instance.get('PublicIpAddress', 'N/A')
            
            # Get private IP
            private_ip = instance.get('PrivateIpAddress', 'N/A')
            
            # Get instance state
            state = instance['State']['Name']
            
            # Get key pair name
            key_name = instance.get('KeyName', 'N/A') 

            instances_info.append({
                'InstanceId': instance['InstanceId'],
                'InstanceType': instance['InstanceType'],
                'PublicIP': public_ip,
                'PrivateIP': private_ip,
                'State': state,
                'KeyPair': key_name,
                'VPC': instance.get('VpcId', 'N/A'),
                'Subnet': instance.get('SubnetId', 'N/A')
            })
    
    return instances_info

# Function to display instance information in a formatted table
def display_instances_table(instances):
    """
    Display instance information in a formatted table.
    
    Args:
        instances (list): List of instance dictionaries.
    """
    if not instances:
        print("No running instances found.")
        return
    
    # Prepare table data
    table_data = []
    headers = ["Instance ID", "Type", "Public IP", "Private IP", "State", "Key Pair"]
    
    for instance in instances:
        table_data.append([
            instance['InstanceId'],
            instance['InstanceType'],
            instance['PublicIP'],
            instance['PrivateIP'],
            instance['State'],
            instance['KeyPair']
        ])
    
    # Display table
    print(tabulate(table_data, headers=headers, tablefmt="grid"))
    print(f"\nTotal running instances: {len(instances)}")

# Main execution block
if __name__ == "__main__":
    # Configure AWS credentials (alternatively, use AWS CLI configured credentials)
    # You can also set these as environment variables or use IAM roles
    # boto3.setup_default_session(profile_name='your-profile-name')
    
    ios.system('clear')
    print("\n")
    print("AWS EC2 Instance Information Fetcher")
    print("-----------------------------------\n")
    
    # Get region input (default to us-east-1)
    region = input("Enter AWS region (default: us-east-1): ") or 'us-east-1'
    
    try:
        instances = get_ec2_instances(region)
        display_instances_table(instances)
    except Exception as e:
        print(f"Error fetching instance information: {str(e)}")

```

#### Python Script to Collect details of AWS Instances with storage

```
import boto3
import os as ios
from tabulate import tabulate

def validate_region(region_name):
    """Validate the AWS region name"""
    valid_regions = [
        'us-east-1', 'us-east-2', 'us-west-1', 'us-west-2',
        'ap-south-1', 'ap-northeast-1', 'ap-northeast-2', 'ap-northeast-3',
        'ap-southeast-1', 'ap-southeast-2', 'ca-central-1',
        'eu-central-1', 'eu-west-1', 'eu-west-2', 'eu-west-3',
        'eu-north-1', 'sa-east-1'
    ]
    return region_name in valid_regions

def get_ec2_instances(region_name='us-east-1'):
    """
    Fetch running EC2 instances with their IP addresses, state, key pair, and storage size.
    """
    if not validate_region(region_name):
        raise ValueError(f"Invalid AWS region: {region_name}")
    
    ec2 = boto3.client('ec2', region_name=region_name)
    
    try:
        response = ec2.describe_instances(
            Filters=[{'Name': 'instance-state-name', 'Values': ['running']}]
        )
    except Exception as e:
        raise Exception(f"AWS API error: {str(e)}")
    
    instances_info = []
    
    for reservation in response['Reservations']:
        for instance in reservation['Instances']:
            # Get root device volume size
            root_device_size = 'N/A'
            if 'BlockDeviceMappings' in instance and instance['BlockDeviceMappings']:
                root_device = instance['BlockDeviceMappings'][0]
                if 'Ebs' in root_device and 'VolumeSize' in root_device['Ebs']:
                    root_device_size = f"{root_device['Ebs']['VolumeSize']} GB"
            
            instances_info.append({
                'InstanceId': instance['InstanceId'],
                'InstanceType': instance['InstanceType'],
                'PublicIP': instance.get('PublicIpAddress', 'N/A'),
                'PrivateIP': instance.get('PrivateIpAddress', 'N/A'),
                'State': instance['State']['Name'],
                'KeyPair': instance.get('KeyName', 'N/A'),
                'StorageSize': root_device_size
            })
    
    return instances_info

def display_instances_table(instances):
    """Display instance information in a formatted table."""
    if not instances:
        print("No running instances found.")
        return
    
    headers = ["Instance ID", "Type", "Public IP", "Private IP", "State", "Key Pair", "Storage Size"]
    table_data = [
        [
            i['InstanceId'], 
            i['InstanceType'], 
            i['PublicIP'], 
            i['PrivateIP'], 
            i['State'], 
            i['KeyPair'],
            i['StorageSize']
        ]
        for i in instances
    ]
    
    print(tabulate(table_data, headers=headers, tablefmt="grid"))
    print(f"\nTotal running instances: {len(instances)}")

if __name__ == "__main__":
    ios.system('clear')
    print("\n")
    # Clear the terminal and print a header
    print("AWS EC2 Instance Information Fetcher")
    print("-----------------------------------\n")
    
    region = input("Enter AWS region (default: us-east-1): ") or 'us-east-1'
    
    try:
        instances = get_ec2_instances(region)
        display_instances_table(instances)
    except Exception as e:
        print(f"\nError: {str(e)}")
        print("\nCommon issues:")
        print("- Invalid AWS region name (e.g., use 'ap-south-1' instead of 'reg-ap-south')")
        print("- AWS credentials not properly configured")
        print("- No internet connection")
        print("- No permissions to describe EC2 instances")

```

#### Python Script to Collect details of AWS Instances with case condition

```

import boto3 
import os as ios
from tabulate import tabulate
import time

# This script fetches and displays information about running EC2 instances in a specified AWS region.
# Function to fetch running EC2 instances
def get_ec2_instances(region_name='us-east-1', state_filter=None):
    """
    Fetch running EC2 instances with their IP addresses, state, and key pair.
    
    Args:
        region_name (str): AWS region to query. Defaults to 'us-east-1'.
        state_filter (str): Instance state to filter by (e.g., 'running', 'stopped'). Defaults to None.
    
    Returns:
        list: List of dictionaries containing instance information.
    """
    # Create EC2 client
    ec2 = boto3.client('ec2', region_name=region_name)
    filters = []
    if state_filter:
        filters.append({'Name': 'instance-state-name', 'Values': [state_filter]})
    response = ec2.describe_instances(Filters=filters) if filters else ec2.describe_instances()
    
    instances_info = []
    
    for reservation in response['Reservations']:
        for instance in reservation['Instances']:
            # Get public IP (if available)
            public_ip = instance.get('PublicIpAddress', 'N/A')
            
            # Get private IP
            private_ip = instance.get('PrivateIpAddress', 'N/A')
            
            # Get instance state
            state = instance['State']['Name']
            
            # Get key pair name
            key_name = instance.get('KeyName', 'N/A') 

            # Calculate total storage in GB
            storage = sum([bdm.get('Ebs', {}).get('VolumeSize', 0) for bdm in instance.get('BlockDeviceMappings', [])])
            
            instances_info.append({
                'InstanceId': instance['InstanceId'],
                'InstanceType': instance['InstanceType'],
                'PublicIP': public_ip,
                'PrivateIP': private_ip,
                'State': state,
                'KeyPair': key_name,
                'Storage(GB)': storage,
                'VPC': instance.get('VpcId', 'N/A'),
                'Subnet': instance.get('SubnetId', 'N/A')
            })
    
    return instances_info

# Function to display instance information in a formatted table
def display_instances_table(instances):
    """
    Display instance information in a formatted table.
    
    Args:
        instances (list): List of instance dictionaries.
    """
    if not instances:
        print("No instances found.")
        return
    
    # Prepare table data
    table_data = []
    headers = ["Instance ID", "Type", "Public IP", "Private IP", "State", "Key Pair", "Storage(GB)"]
    
    for instance in instances:
        table_data.append([
            instance['InstanceId'],
            instance['InstanceType'],
            instance['PublicIP'],
            instance['PrivateIP'],
            instance['State'],
            instance['KeyPair'],
            instance['Storage(GB)']
        ])
    
    # Display table
    print(tabulate(table_data, headers=headers, tablefmt="grid"))
    print(f"\nTotal instances: {len(instances)}")

# Function to start stopped instances
def start_instances(region_name):
    """
    Start all stopped EC2 instances in the specified region.
    
    Args:
        region_name (str): AWS region to start instances in.
    """
    ec2 = boto3.client('ec2', region_name=region_name)
    stopped = get_ec2_instances(region_name, state_filter='stopped')
    if not stopped:
        print("No stopped instances to start.")
        return
    
    display_instances_table(stopped)
    ids = [inst['InstanceId'] for inst in stopped]
    confirm = input(f"Start all stopped instances ({', '.join(ids)})? (y/n): ").lower()
    if confirm == 'y':
        ec2.start_instances(InstanceIds=ids)
        print("Starting instances... Waiting 45 seconds for instances to initialize.")
        time.sleep(45)
        print("Fetching updated instance details...")
        # Fetch and display updated details for the started instances
        updated_instances = get_ec2_instances(region_name, state_filter='running')
        # Filter only the instances we just started
        started_instances = [inst for inst in updated_instances if inst['InstanceId'] in ids]
        display_instances_table(started_instances)
    else:
        print("Operation cancelled.")

# Function to stop running instances
def stop_instances(region_name):
    """
    Stop all running EC2 instances in the specified region.
    
    Args:
        region_name (str): AWS region to stop instances in.
    """
    ec2 = boto3.client('ec2', region_name=region_name)
    running = get_ec2_instances(region_name, state_filter='running')
    if not running:
        print("No running instances to stop.")
        return
    
    display_instances_table(running)
    ids = [inst['InstanceId'] for inst in running]
    confirm = input(f"Stop all running instances ({', '.join(ids)})? (y/n): ").lower()
    if confirm == 'y':
        ec2.stop_instances(InstanceIds=ids)
        print("Stopping instances... Waiting 45 seconds for instances to stop.")
        time.sleep(45)
        print("Fetching updated instance details...")
        # Fetch and display updated details for the stopped instances
        updated_instances = get_ec2_instances(region_name, state_filter='stopped')
        # Filter only the instances we just stopped
        stopped_instances = [inst for inst in updated_instances if inst['InstanceId'] in ids]
        display_instances_table(stopped_instances)
    else:
        print("Operation cancelled.")

# Function to check the status of all instances
def check_status(region_name):
    """
    Check and display the status of all EC2 instances in the specified region.
    
    Args:
        region_name (str): AWS region to check instance status in.
    """
    all_instances = get_ec2_instances(region_name)
    display_instances_table(all_instances)

# Main execution block
if __name__ == "__main__":
    # Configure AWS credentials (alternatively, use AWS CLI configured credentials)
    # You can also set these as environment variables or use IAM roles
    # boto3.setup_default_session(profile_name='your-profile-name')
    
    ios.system('clear')
    print("\nAWS EC2 Instance Control Panel")
    print("-----------------------------------\n")
    
    # Get region input (default to us-east-1)
    region = input("Enter AWS region (default: us-east-1): ") or 'us-east-1'
    
    print("\nChoose an option:")
    print("1. Start stopped instances & show details")
    print("2. Stop running instances")
    print("3. Check status of all instances")
    choice = input("Enter your choice (1/2/3): ").strip()
    try:
        match choice:
            case "1":
                start_instances(region)
                print("\nUpdated instance details:")
                check_status(region)
            case "2":
                stop_instances(region)
                print("\nUpdated instance details:")
                check_status(region)
            case "3":
                check_status(region)
            case _:
                print("Invalid choice.")
    except Exception as e:
        print(f"Error: {str(e)}")


```
