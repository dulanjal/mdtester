## Package overview

This package exposes functionality required to handle user authentication and authorization. 

### Authentication
There are two out-of-the-box authentication mechanisms:
Username and password based authentication
JWT based authentication

#### Username and password based authentication
A user is authenticated based on the username and the password that are stored in a userstore. A userstore could be of a type such as JDBC, LDAP and file-based. By default auth package contains a file-based userstore implementation and it is possible to implement a different type of a userstore by extending the struct UserStore.

#### JWT based authentication
A user is authenticated by validating a JWT in possession. 

### Authentication
User authorization is based on the concepts of scopes and groups. A scope is a particular permission that is needed to access a protected resource. A group consists of set of scopes, and users are assigned to a set of groups. Therefore, when trying to access a protected resource, a user is authorized based on the scope that

## Samples





