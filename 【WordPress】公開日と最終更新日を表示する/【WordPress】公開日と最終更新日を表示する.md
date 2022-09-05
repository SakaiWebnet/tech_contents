# 参考サイト
>[【WordPress】公開日と最終更新日を表示する | WEBDESIGNDAY](https://webdesignday.jp/inspiration/wordpress/3683/#i-2)

# content-single.phpのバックアップ

# コード
content-single.php
```php
	<header class="entry-header">
		<?php the_title('<h1 class="entry-title">', '</h1>'); ?>
		<time>
			<!--公開日:<?php the_time('Y/m/d'); ?><br>-->           # テンプレートで元々の公開日を使用するため非表示にするためにコメントアウト
			<?php if (get_the_time('Y/m/d') != get_the_modified_date('Y/m/d')) : ?>
				最終更新日:<?php the_modified_date('Y/m/d') ?>
			<?php endif; ?>
		</time>
	</header><!-- .entry-header -->
```