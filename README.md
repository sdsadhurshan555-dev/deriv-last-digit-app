<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Deriv Last Digit</title>

  <style>
    body {
      font-family: Arial, sans-serif;
      background: #111827;
      color: white;
      text-align: center;
      padding: 25px;
    }

    .box {
      max-width: 420px;
      margin: auto;
      background: #1f2937;
      padding: 25px;
      border-radius: 18px;
    }

    h1 {
      margin-bottom: 25px;
    }

    .price {
      font-size: 32px;
      margin: 20px 0;
    }

    .digit {
      font-size: 70px;
      font-weight: bold;
      margin: 20px;
    }

    button {
      padding: 14px 25px;
      border: none;
      border-radius: 10px;
      font-size: 18px;
      cursor: pointer;
    }

    #status {
      margin-top: 15px;
      color: #9ca3af;
    }
  </style>
</head>

<body>

  <div class="box">

    <h1>Deriv Last Digit</h1>

    <div>Live Price</div>
    <div class="price" id="price">--</div>

    <div>Last Digit</div>
    <div class="digit" id="digit">--</div>

    <button onclick="connectDeriv()">Connect Deriv</button>

    <div id="status">Not connected</div>

  </div>

<script>

let ws;

function connectDeriv() {

  document.getElementById("status").innerText =
    "Connecting...";

  ws = new WebSocket(
    "wss://ws.derivws.com/websockets/v3?app_id=1089"
  );

  ws.onopen = function() {

    document.getElementById("status").innerText =
      "Connected";

    ws.send(JSON.stringify({
      ticks: "R_100",
      subscribe: 1
    }));

  };

  ws.onmessage = function(event) {

    const data = JSON.parse(event.data);

    if (data.error) {

      document.getElementById("status").innerText =
        "Error: " + data.error.message;

      return;
    }

    if (data.tick) {

      const quote = data.tick.quote;

      document.getElementById("price").innerText =
        quote;

      const text = String(quote);

      const lastDigit =
        text.replace(".", "").slice(-1);

      document.getElementById("digit").innerText =
        lastDigit;

    }

  };

  ws.onerror = function() {

    document.getElementById("status").innerText =
      "Connection error";

  };

  ws.onclose = function() {

    document.getElementById("status").innerText =
      "Disconnected";

  };

}

</script>

</body>
</html>
