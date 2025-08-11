# Escalación de privilegios 

Tags: #Hacking #CloudComputing  #AWS 

## AWS CLI

```bash 
# Kali Tool
❯ vim user-policy.json        # Crear una politica 
	{ 
		"Version":"2025-10-17",
		"Statement": [
	      { 
			"Effect":"Allow",
			"Action":"*",
			"Resource":"*"
	      }
	   ]
	}


# Kali Tool
❯ aws iam create-policy --policy-name user-policy --policy-document file://user-policy.json  
❯ aws iam attach-user-policy --user-name Username --policy-arn arn:aws:iam::Account ID:policy/user-policy 
❯ aws iam list-attach-user-policies --user-name Username 
❯ aws iam list-users 
❯ aws s3api list-buckets --query "Buckets_Name"
❯ aws iam list-user-policies 
❯ aws iam list-role-policies
❯ aws iam list-group-policies 
❯ aws iam create-user 
```