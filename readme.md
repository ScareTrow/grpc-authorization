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


## Dependencies for development

* [golangci-lint](https://golangci-lint.run/) -- To keep the code in good condition
* [protoc](https://github.com/protocolbuffers/protobuf/releases/tag/v24.4) + [proto-gen-go](google.golang.org/protobuf/cmd/protoc-gen-go@v1.28) & [protoc-gen-go-grpc](google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2) -- To compile proto files
* [pre-commit](https://pre-commit.com/) -- To eliminate the possibility of committing dirty code
* [docker](https://docs.docker.com/get-started/)(with compose) -- To deploy the environment
* [make](https://www.gnu.org/software/make/) -- To simplify working with the environment

## Startup

Before starting the project, you need to specify the necessary environment variables.
Docker compose uses an `.env` file, an example of which can be found in `.env.example`.

After creating the `.env` file, you can run the project using the `make run` command,
which will start the project via docker compose.

## Testing

The project implements unit tests for `MemoryRepository` and
end-to-end end-to-end tests for the whole application.



### Unit tests

Unit tests are started by the `make test-unit` command.

### End-to-end tests

For end-to-end tests, the application is run in an isolated docker container,
using [testcontainers](https://testcontainers.com/).
The tests themselves are called from the host system.

__end-to-end tests do not read the .env file, so you need to manually set environment variables.__

End-to-end tests are started by the `make test-e2e` command.

## Application Architecture

The `proto` directory contains the description of the gRPC service and the generated code.

The `internal` directory contains the main application code.

The `tests` directory contains the end-to-end tests.

The application uses a clean architecture, with the following layers:
* transport -- presentation layer: serving gRPC requests
* usecases -- business logic layer
* infrastructure -- data source: database accesses

### Alternative structure

The reason for choosing such a directory structure is the fact that
that this application is a primitive CRUD interface for only one entity.
single entity. In an application with multiple units and complex business logic, the
the architecture could look like this:

```
internal/
├─── main.go
├─── common/
│ ├─── context_logger.go
│ ├──── disabled_logger.go
│ └─── flagged_error.go
└─── user/
    ├─── models.go
    ├─── usecases.go
    ├─── handlers.go
    ├─── repository.go
    └─── repository_test.go
```

## Solutions and notes

* A simple error handling mechanism has been implemented in the internal/common package to handle domain errors, as described by Nate Finch here [Error Flags]().
a simple error handling mechanism described by Nate Finch here [Error Flags](https://npf.io/2021/04/errorflags/).

* Since the project is a primitive CRUD interface and do not contains
complex business logic, it was decided to abandon the business logic layer
and use a regular anemic model to represent the data in the application.

* The application's API follows the CQS principle, so api commands such as
creating and modifying a user, return an empty object.

* Sync.Map was chosen to store the data because it is thread-safe

* Since sync.Map does not provide a way to get the number of records,
MemoryRepository.GetAll creates an empty slice and on each iteration it expands it if necessary (append).
this has a negative impact on performance.
We probably could count the number of records in a separate var, but why? :D
* Since you have to log in to create a user,
as an administrator, it was decided to create the first administrator
at application startup using values from environment variables.

* To encrypt stored user passwords, bcrypt is used