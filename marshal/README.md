gorails/marshal
===============

[![Build Status](https://travis-ci.org/adeven/gorails.png)](https://travis-ci.org/adeven/gorails)

## Installation

With Go and git installed:

```
go get -u github.com/adeven/gorails/marshal
```

Or you can use [Goem](http://big-elephants.com/2013-09/goem-the-missing-go-extension-manager/).

## Usage

```go
import (
  "errors"
  "github.com/adeven/gorails/marshal"
)

func getAuthUserId(decrypted_session_data []byte) (user_id int64, err error) {
  unauthorized_user := errors.New("Unauthorized user")
  invalid_auth_data := errors.New("Invalid auth data")

  session_data, err := marshal.CreateMarshalledObject(decrypted_session_data).GetAsMap()
  if err != nil {
    return
  }

  warden_data, ok := session_data["warden.user.user.key"]
  if !ok {
    return 0, unauthorized_user
  }

  warden_user_key, err := warden_data.GetAsArray()
  if err != nil {
    return
  }
  if len(warden_user_key) < 1 {
    return 0, invalid_auth_data
  }

  user_data, err := warden_user_key[0].GetAsArray()
  if err != nil {
    return
  }
  if len(user_data) < 1 {
    return 0, invalid_auth_data
  }

  user_id, err = user_data[0].GetAsInteger()

  return
}
```
