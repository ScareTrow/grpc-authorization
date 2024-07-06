You need to write a web service that stores user profiles and authorizes them.

Profile has a set of fields:
1. id (uuid, unique)
2. email
3. username (unique)
4. password
5. admin (bool)

The service must have a handle set (gRPC):
User creation,
Issuing a list of users
Issuing user by id
Modify and delete profile

The service uses basic access authentication (https://en.wikipedia.org/wiki/Basic_access_authentication).

All registered users can view profiles.
Admin can create, modify and delete profiles.

To store profile data we need to implement a primitive in memory database without using third party solutions.

The task is not difficult, we really want to see how you write code.

Time is 1 working week, if you can do it sooner - great).

The task can be done with varying degrees of depth. If you realize that you won't have time to do it better, you should specify all the important problems and assumptions in the README file.

# Notes

1. Since sync.Map does not provide a way to get the number of records,
MemoryRepository.GetAll creates an empty slice and on each iteration it expands it if necessary (append).
this has a negative impact on performance.
We probably could count the number of records in a separate var, but why? :D
