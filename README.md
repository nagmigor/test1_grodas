# test1_grodas

Ссылка на тестовое задание http://dev.grodas.com.ua/

1. На CMS ocStore 2.х (OpenCart - тоже приемлемо. Версию можно и 3.х, несущественно) в настройках категорий есть 

редактирование текстового поля “Описание”. Добавить аналогичное ему второе описание (т.е. в админке должны 

редактироваться и сохраняться 2 разных поля для каждого языка). Редактирование и оформление сделать аналогично 

существующему.

Это задание на умение работать с уже готовым и отлаженным продуктом, т.е. тут тестируется не фантазия и 

изобретательность а умение корректно продублировать + промодифицировать существующий функционал не сломав при 

этом ничего. В идеале - при чтении кода я не должен видеть разницы между эталонным стилем опенкарта и добавленным кодом.

2. В клиентской части организовать вывод второго описания в футере сайта. Логика "если мы зашли в категорию (к примеру 

в /index.php?route=product/category&path=20_27) то вывести в футер наше дополнительное описание" 

Единственного правильного решения тут нет, я видел вариантов как реализовать это уже штук 10 в разной степени 

корректности и упоротости, вот тут как-раз то и тестируем “как обойти ограничения существующей архитектуры чтобы не
 
сильно ее при этом порушить”.


https://docs.google.com/document/d/1y7vZWfQJyRWmbjkj6w5vY9FMlb4WML18rujZyNJeTDQ/edit


###		Выполнение задания 1.

1. Добавляем в таблицу oc_category_description поле description_2

2. Добавляем это поле в модель admin\model\catalog\category.php

	в метод addCategory($data) строка 13

	$this->db->query("INSERT INTO " . DB_PREFIX . "category_description SET category_id = '" . (int)$category_id 

		. "', language_id = '" . (int)$language_id . "', name = '" . $this->db->escape($value['name']) 

		. "', description = '" . $this->db->escape($value['description']) 
		
###		. "', description_2 = '" . $this->db->escape($value['description_2']) 
		
		. "', meta_title = '" . $this->db->escape($value['meta_title']) 

		. "', meta_h1 = '" . $this->db->escape($value['meta_h1']) 

		. "', meta_description = '" 

		. $this->db->escape($value['meta_description']) 

		. "', meta_keyword = '" . $this->db->escape($value['meta_keyword']) . "'");
		
	в метод editCategory($category_id, $data) строка 67

	$this->db->query("INSERT INTO " . DB_PREFIX . "category_description SET category_id = '" . (int)$category_id 

		. "', language_id = '" . (int)$language_id . "', name = '" . $this->db->escape($value['name']) 

		. "', description = '" . $this->db->escape($value['description']) 
		
###		. "', description_2 = '" . $this->db->escape($value['description_2']) 
		
		. "', meta_title = '" . $this->db->escape($value['meta_title']) 

		. "', meta_h1 = '" . $this->db->escape($value['meta_h1']) 

		. "', meta_description = '" 

		. $this->db->escape($value['meta_description'])

		. "', meta_keyword = '" . $this->db->escape($value['meta_keyword']) . "'");

		
	в метод getCategoryDescriptions($category_id) строка 268

		foreach ($query->rows as $result) {

			$category_description_data[$result['language_id']] = array(

				'name'             => $result['name'],

				'meta_title'       => $result['meta_title'],

				'meta_h1'      	   => $result['meta_h1'],

				'meta_description' => $result['meta_description'],

				'meta_keyword'     => $result['meta_keyword'],

				'description'      => $result['description'],

				
###				'description_2'      => $result['description_2']
			);
		}
3. Добавляем в файле admin\language\ru-ru\catalog\category.php строку 20

###		$_['entry_description_2']      = 'Описание 2';
		
   Добавляем в файле admin\language\ru-ru\catalog\information.php строку 20

###		$_['entry_description_2']      = 'Описание 2:';	
		
   Добавляем в файле admin\language\ru-ru\catalog\manufacturer.php строку 27

###		$_['entry_description_2']      = 'Описание 2';	
		
   Добавляем в файле admin\language\ru-ru\catalog\product.php строку 32

###		$_['entry_description_2']      = 'Описание 2';
 
		
4. Добавляем это поле в контроллере admin\controller\catalog\category.php

	в метод getForm() строка 321

###	$data['entry_description_2'] = $this->language->get('entry_description_2');
	
	
5. Добавляем вывод формы для этого поля в файле admin\view\template\catalog\category_form.tpl строки 59-65

<div class="form-group">

    <label class="col-sm-2 control-label" for="input-description_2<?php echo $language['language_id']; ?>">

                                                                <?php echo $entry_description_2; ?></label>
															
    <div class="col-sm-10">

        <textarea name="category_description[<?php echo $language['language_id']; ?>][description_2]" 

				  placeholder="<?php echo $entry_description_2; ?>" id="input-description_2

				  <?php echo $language['language_id']; ?>" data-lang="<?php echo $lang; ?>" 

				  class="form-control summernote">

				  <?php echo isset($category_description[$language['language_id']]) ? 

						$category_description[$language['language_id']]['description_2'] : ''; ?>

		</textarea>
    </div>

 </div>

 
###		Выполнение задания 2.
		
1. Инициализируем переменную $description_2 в файле catalog\controller\product\category.php	строка 143

###	$data['description_2'] = html_entity_decode($category_info['description_2'], ENT_QUOTES, 'UTF-8'); 
	
###	$this->description_2 = $data['description_2'];

2. В файле catalog\controller\common\footer.php передаем "Описание 2" в footer строка 75

###		$data['description_2'] = $this->description_2;

3. Для вывода Описания 2 вносим изменения в представление футура - файл catalog\view\theme\default\template\common\footer.tpl строки 42 - 45
```

	<div class="col-sm-2">
        
		<p><?php echo $powered; ?></p>
		
  </div>

  <div class="col-sm-10"><?php echo $description_2?></div>

```