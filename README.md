# RxWind

## Conception

* RxGraph: change propagation graph
* RxVar: atomic mutable value
* RxExpr: expression of RxVars

```scala
RxGraph { graph =>
    val name = RxVar("YourName")
    val uuid = RxExpr {
        name().map(_ => UUID.getNew)
    }
}
```

```scala
RxGraph { graph =>
    val credentials = RxVar {
        Credentials(
            firstName = "YourFirstName",
            lastName = "YourLastName",
            passwordHash = bcrypt.encode("password")
        )
    }
    val databaseConnection = RxVar {
        sqlStore.connect
    }
    val token = RxExpr {
        credentials() map {
            case Credentials(firstName, lastName, passwordHash) => {
                if (
                    bcrypt.encode(s"$prefix$passwordHash$suffix") ==
                        sqlStore.connect.map(_.getPassword)
                ) {
                    UUID.getNew
                } else {
                    new PasswordMismatch
                }
            }
        }
    }
}
```
