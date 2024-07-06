You need to write a web service that stores user profiles and authorizes them.

Profile has a set of fields:

id (uuid, unique)
email
username (unique)
password
admin (bool)
The service must have a handle set (gRPC): User creation, Issuing a list of users Issuing user by id Modify and delete profile

The service uses basic access authentication

All registered users can view profiles. Admin can create, modify and delete profiles.

To store profile data we need to implement a primitive in memory database without using third party solutions.

The task is not difficult, we really want to see how you write code.

The task can be done with different degrees of depth. If you realize that you won't have time to do it better, you should specify all important issues and assumptions in the README file