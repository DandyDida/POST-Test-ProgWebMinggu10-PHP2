# POST-Test-ProgWebMinggu10-PHP2

home.php
<?php
require "koneksi.php";
// isi disini
session_start();

if (!isset($_SESSION["username"])) {
    header("Location: login.php");
    exit();
}

$username = $_SESSION["username"];

// Ambil data pengguna
$sql_user = "SELECT id_users, nama_lengkap, no_rekening FROM users WHERE username = ?";
$stmt_user = $conn->prepare($sql_user);
$stmt_user->bind_param("s", $username);
$stmt_user->execute();
$result_user = $stmt_user->get_result();
$user = $result_user->fetch_assoc();
$id_users = $user["id_users"];

// Ambil data transaksi sesuai ID user
$sql_transaksi = "SELECT item, pemasukan, pengeluaran, saldo, tanggal FROM transaksi WHERE id_users = ?";
$stmt_transaksi = $conn->prepare($sql_transaksi);
$stmt_transaksi->bind_param("i", $id_users);
$stmt_transaksi->execute();
$result_transaksi = $stmt_transaksi->get_result();
?>

<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8" />
    <title>Dashboard Keuangan</title>
    <style>
        body {
            font-family: 'Segoe UI', sans-serif;
            background: #f0f4f8;
            margin: 0;
            padding: 20px;
        }
        .container {
            background: #fff;
            max-width: 900px;
            margin: 0 auto;
            padding: 30px 40px;
            border-radius: 12px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.08);
        }
        h2, h3 {
            color: #333;
        }
        p {
            font-size: 16px;
            color: #555;
        }
        table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }
        th, td {
            padding: 12px 10px;
            border: 1px solid #ddd;
            text-align: center;
        }
        th {
            background-color: #4CAF50;
            color: white;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        .logout-btn {
            display: inline-block;
            margin-top: 30px;
            padding: 10px 20px;
            background-color: #e74c3c;
            color: #fff;
            text-decoration: none;
            border-radius: 6px;
            font-weight: bold;
        }
        .logout-btn:hover {
            background-color: #c0392b;
        }
    </style>
</head>
</head>
<body>
    <div class="container">
        <h2>Selamat Datang, <?php echo htmlspecialchars($user["nama_lengkap"]); ?>!</h2>
        <p>No. Rekening: <strong><?php echo htmlspecialchars($user["no_rekening"]); ?></strong></p>
        <h3>Riwayat Transaksi</h3>
        <table>
            <thead>
                <tr>
                    <th>Item</th>
                    <th>Pemasukan</th>
                    <th>Pengeluaran</th>
                    <th>Saldo</th>
                    <th>Tanggal</th>
                </tr>
            </thead>
            <tbody>
            <?php while ($row = $result_transaksi->fetch_assoc()) { ?>
                    <tr>
                        <td><?php echo htmlspecialchars($row["item"]); ?></td>
                        <td>Rp <?php echo number_format($row["pemasukan"], 2, ',', '.'); ?></td>
                        <td>Rp <?php echo number_format($row["pengeluaran"], 2, ',', '.'); ?></td>
                        <td>Rp <?php echo number_format($row["saldo"], 2, ',', '.'); ?></td>
                        <td><?php echo date("d-m-Y H:i", strtotime($row["tanggal"])); ?></td>
                    </tr>
                <?php } ?>
            </tbody>
        </table>

        <center><a href="logout.php" class="logout-btn">Logout</a></center>
    </div>
</body>
</html>


koneksi.php

<?php
// isi disini
$servername = "localhost";
$username = "root";
$password = "";
$dbname = "soalb"; 

$conn = new mysqli($servername, $username, $password, $dbname);

// Periksa koneksi
if ($conn->connect_error) {
    die("Koneksi gagal: " . $conn->connect_error);
}

?>

login.php

<?php
require "koneksi.php"; 
// isi disini
session_start();

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = $_POST["password"];

    $sql = "SELECT * FROM users WHERE username = ? AND password = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param("ss", $username, $password);
    $stmt->execute();
    $result = $stmt->get_result();

    if ($result->num_rows > 0) {
        $_SESSION["username"] = $username;
        header("Location: home.php");
        exit();
    } else {
        $error = "Username atau kata sandi salah!";
    }
}
?>

<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
    <title>Login Keuangan</title>
    <style>
        * {
            box-sizing: border-box;
        }
        body {
            font-family: 'Segoe UI', sans-serif;
            background: linear-gradient(135deg, #74ebd5, #ACB6E5);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .login-container {
            background-color: #fff;
            padding: 30px 40px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            width: 100%;
            max-width: 400px;
            text-align: center;
        }
        .login-container h2 {
            margin-bottom: 20px;
            color: #333;
        }
        .login-container img {
            width: 80px;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
            text-align: left;
        }
        .form-group label {
            display: block;
            font-size: 14px;
            margin-bottom: 5px;
            color: #444;
        }
        .form-group input {
            width: 100%;
            padding: 10px 12px;
            border: 1px solid #ccc;
            border-radius: 6px;
        }
        .login-btn {
            width: 100%;
            padding: 12px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            cursor: pointer;
        }
        .login-btn:hover {
            background-color: #45a049;
        }
        .alert {
            color: #d8000c;
            background-color: #ffbaba;
            padding: 10px;
            margin-bottom: 15px;
            border-radius: 6px;
        }
    </style>
</head>
<body>
    <div class="login-container">
    <h2>Login Keuangan</h2>
        <img src="https://cdn-icons-png.flaticon.com/512/3135/3135789.png" alt="User">
        <?php if (isset($error)) { echo "<div class='alert'>$error</div>"; } ?>
        <form method="post" action="login.php">
            <div class="form-group">
                <label for="username">Username</label>
                <input type="text" id="username" name="username" required>
            </div>
            <div class="form-group">
                <label for="password">Kata Sandi</label>
                <input type="password" id="password" name="password" required>
            </div>
            <button type="submit" class="login-btn">Login</button>
        </form>
    </div>
</body>
</html>

Logout.php

<?php
// isi disini
session_start();
session_unset();
session_destroy();

header("Location: login.php"); // Redirect ke halaman login setelah logout
exit();
?>





