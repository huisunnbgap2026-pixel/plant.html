<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>plant test</title>
  <style>
    body{
      font-family: Arial, sans-serif;
      padding:40px;
      background:#f6f2e8;
    }
  </style>
</head>
<body>
  <h1>plant.html 正常</h1>
  <p id="msg"></p>

  <script>
    const url = new URL(window.location.href);
    const plant = url.searchParams.get("plant") || "";
    const page = url.searchParams.get("page") || "";
    document.getElementById("msg").textContent =
      plant || page ? `plant=${plant}` : "缺少植物資料，請重新掃描 QR code。";
  </script>
</body>
</html>
