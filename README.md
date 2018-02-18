<?php
// Если был получен POST-запрос с файлом, то проверяем, подходит ли он
if (isset($_POST['upload'])) {

	  // Редирект на страницу с тестами через три секунды
    header('refresh:3; url=list.php');
	
    // Определяем массив со всеми файлами из папки с тестами
    if (!empty(glob('tests/*.json'))) {
        $allFiles = glob('tests/*.json');
    } else {
        $allFiles = [0];
    }
    // Определяем загружаемый файл
    $uploadfile = 'tests/' . basename($_FILES['testfile']['name']);
	
    // Проверяем файл по if, если не подходит - выдаем ошибку
    if (pathinfo($_FILES['testfile']['name'], PATHINFO_EXTENSION) !== 'json') {
        $result = "<p class='error'>Можно загружать файлы только с расширением json</p>";
    } else if ($_FILES["testfile"]["size"] > 1024 * 1024 * 1024) {
        $result = "<p class='error'>Размер файла превышает три мегабайта</p>";
    } else if (in_array($uploadfile, $allFiles, true)) {
        $result = "<p class='error'>Файл с таким именем уже существует.</p>";
    } else if (move_uploaded_file($_FILES['testfile']['tmp_name'], $uploadfile)) {
        $result = "<p class='success'>Файл корректен и успешно загружен на сервер</p>";
    } else {
        $result = "<p class='error'>Произошла ошибка</p>";
    }
}
?>

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>2.2 «Обработка форм»</title>
</head>
<body>

<!-- Если файл был отправлен, то выводить информацию о файле и уведомление об успешной загрузке/ошибке -->

<?php if (isset($_POST['upload'])): ?>
    <a href="<?php $_SERVER['HTTP_REFERER'] ?>"></a>
    <?php echo $result; ?>
       <pre>
        <?php print_r($allFiles); ?>
        <hr>
        <?php print_r($_FILES); ?>
    </pre>
<?php endif; ?>

<!-- Пока файл или форма теста не была отправлена, выводить форму загрузки теста -->

<?php if (!isset($_POST['create']) && !isset($_POST['upload'])): ?>

    <form id="load-json" method="POST" enctype="multipart/form-data">
        <fieldset>
            <legend>Загрузите тест в формате json</legend>
            <input type="file" name="testfile" id="uploadfile" required>
            <input type="submit" value="Отправить" id="submit-upload" name="upload">
        </fieldset>
    </form>

    <div class="all-tests">
        <fieldset>
            <a href="list.php">Посмотреть все тесты >></a>
        </fieldset>
    </div>

   

<?php endif; ?>

</body>
</html>
# 2.3-PHP-HTTP
