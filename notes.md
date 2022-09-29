bcrypt.hash:

```js
bcrypt.hash('somepassword', 10, (err, hash) => {})
```

- is a method

- trying to `return` a value from inside the funciton `(err, hash) => {}` wont return a value, as we aren't calling it.
it is just a method that runs, and we can add some code that gets executed from inside this callback.

```js
await bcrypt.hash(req.body.password, 10, (err, hash) => {
  console.log(hash);
});
```
the log runs,

but,

```js
await bcrypt.hash(req.body.password, 10, (err, hash) => {
  return hash;
});
```
and setting it to a variable

```js
const hashpassword = await bcrypt.hash(req.body.password, 10, (err, hash) => {
  return hash;
});
console.log(hashpassword);
```
hashpassword is `undefined`.

now we can do:

```js
const hashpassword = await bcrypt.hash(req.body.password, 10);
```
`hashpassword` will sucessfully be a value
But i will not have access to error handling in this approach.


* conclusion

```js
app.post('/sign-up', async (req, res, next) => {
  await bcrypt.hash(req.body.password, 10, (err, hash) => {
    if (err) {
      return err;
    }
    const user = new User({
      username: req.body.username,
      password: hash,
    }).save((err) => {
      if (err) {
        return next(err);
      }
      res.redirect('/');
    });
  });
});
```
this might be the best approach, as I will retain my ability to use error handling for generating the hash via `.hash()`