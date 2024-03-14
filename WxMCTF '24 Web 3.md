Writeup by Colin

Flask source:
```python
@app.route("/send", methods=["POST"])
def send_data():
    raw_data = request.get_data()
    recipient = request.form.get("recipient");
    amount = request.form.get("amount");

    if (amount == None or (not amount.isdigit()) or int(amount) < 0 or recipient == None or recipient == "Eatingfood"):
        return redirect("https://media.tenor.com/UlIwB2YVcGwAAAAC/waah-waa.gif")
    
    # Send the data to the Apache PHP server
    raw_data = b"sender=Eatingfood&" + raw_data;
    requests.post("http://localhost:80/gateway.php", headers={"content-type": request.headers.get("content-type")}, data=raw_data)
    return redirect("/check-balance")
```

PHP source:
```php
if ($_SERVER["REQUEST_METHOD"] === "POST") {
    $json = file_get_contents('accounts.json');
    $json_data = json_decode($json,true);

    $json_data[$_POST['recipient']] += $_POST['amount'];
    $json_data[$_POST['sender']] -= $_POST['amount'];
    
    file_put_contents('accounts.json', json_encode($json_data));
}
```

The crux of this problem is bypassing the Flask server's sanitization and getting something akin to `sender=LostCactus&recipient=Eatingfood` PHP server.

Luckily for us, Flask and this Apache PHP server process duplicate keys in form data differently.
Given this form data:
```
key1=val1&key1=val2
```
Flask will think that `key1=val1`, while Apache will think that `key1=val2`.
We use this to bypass the filter:

`sender=Eatingfood&recipient=a&sender=LostCactus&recipient=Eatingfood`

And now Eatingfood can have food to eat. Hooray!

