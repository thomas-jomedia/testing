# COOKBOOK #

## Move an old controller to the new structure ##


	//public_html/index.php
	
	<?php
	
	require_once __DIR__ . '/../vendor/autoload.php'; //We need the auto load from composer
	require_once __DIR__ . '/../app/MyApp.php';//This file is not specified in composer we need to load it by hand
	
	MyApp::createFromGlobalContext()->run();
