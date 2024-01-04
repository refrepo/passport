# express 集成

- `passport.authenticate` 会返回一个 `express/connect` 形式的中间件回调

```js
const passport = require("passport");
// 在 passport._strategies 字典上定义一个认证策略
// 其中的 verify 是具体策略的配置
passport.use(
  new LocalStrategy(function verify(username, password, cb) {
    db.get("SELECT * FROM users WHERE username = ?", [username], function (err, row) {
      if (err) {
        return cb(err);
      }
      if (!row) {
        return cb(null, false, { message: "Incorrect username or password." });
      }

      crypto.pbkdf2(password, row.salt, 310000, 32, "sha256", function (err, hashedPassword) {
        if (err) {
          return cb(err);
        }
        if (!crypto.timingSafeEqual(row.hashed_password, hashedPassword)) {
          return cb(null, false, { message: "Incorrect username or password." });
        }
        return cb(null, row);
      });
    });
  })
);
router.post(
  "/login/password",
  passport.authenticate("local", {
    successReturnToOrRedirect: "/",
    failureRedirect: "/login",
    failureMessage: true,
  })
);
```
