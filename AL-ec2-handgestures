import boto3
import cv2
from cvzone.HandTrackingModule import HandDetector

# Initialize AWS credentials
aws_access_key_id = 'AKIATGISPEPPKTZ7TDDM'
aws_secret_access_key = 'H4ow/g8WyH8nurR1xEoxvLyq0CQYQej2i/XzFt34'
region_name = 'ap-south-1'  # Replace with your desired AWS region

# Create an EC2 client
ec2_client = boto3.client('ec2', region_name=region_name,
                          aws_access_key_id='AKIATGISPEPPKTZ7TDDM',
                          aws_secret_access_key='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')

def launch_os():
    # Define the parameters for the instance
    instance_params = {
        'ImageId': 'ami-0a2acf24c0d86e927',  # Replace with the desired Amazon Machine Image (AMI) ID
        'InstanceType': 't2.micro',  # Replace with the desired instance type
        'MinCount': 1,
        'MaxCount': 1,
        'SecurityGroupIds': ['sg-0af0b5092d18259bf'],  # Replace with the desired security group ID(s)
        # Add any additional parameters as needed
    }

    # Launch the instance
    response = ec2_client.run_instances(**instance_params)
    instance_id = response['Instances'][0]['InstanceId']

    print(f"Instance {instance_id} launched successfully!")
    return instance_id

def delete_os(instance_id):
    ec2 = boto3.resource('ec2', region_name=region_name,
                         aws_access_key_id=aws_access_key_id,
                         aws_secret_access_key=aws_secret_access_key)

    # Terminate the specified instance
    ec2.instances.filter(InstanceIds=[instance_id]).terminate()

detector = HandDetector(maxHands=1, detectionCon=0.8)
allOS = []
cap = cv2.VideoCapture(0)

while True:
    ret, photo = cap.read()
    hand = detector.findHands(photo, draw=False)
    if hand:
        detectHand = hand[0]
        if detectHand:
            finger_up = detector.fingersUp(detectHand)
            if detectHand['type'] == 'Left':
                # Check if any finger on the left hand is raised
                if any(finger_up):
                    # Launch a new OS instance
                    new_os_id = launch_os()
                    allOS.append(new_os_id)
            else:
                # Check if the second finger on the right hand is raised
                if finger_up[1] == 1:
                    # Terminate the last launched OS instance
                    if allOS:
                        last_os_id = allOS.pop()
                        delete_os(last_os_id)

    cv2.imshow("my photo", photo)
    if cv2.waitKey(2000) == 27:  # Exit loop after 2 seconds on pressing 'Esc' (key code 27)
        break

cv2.destroyAllWindows()
cap.release()
