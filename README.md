# csv-import-in-laravel-with-array

		
If You want to insert Data in your database from an excel file with Text,select,number field then you can try this. 
			
1st install this Package
		
		composer require maatwebsite/excel
		

Now open config/app.php file and add service provider and aliase.

config/app.php

		'providers' => [

			....

			Maatwebsite\Excel\ExcelServiceProvider::class,
		],

		'aliases' => [
			....

			'Excel' => Maatwebsite\Excel\Facades\Excel::class,
			],

routes/web.php

		Route::get('importExportView', 'YourController@importExportView');
		Route::post('import', 'YourController@import')->name('import');	

		
Create Import Class, Run this command in your terminal:	

		php artisan make:import ClassName --model=YourModel
		
DataImport class Code View		
		<?php
   
		namespace App\Imports;

		use App\User;
		use Maatwebsite\Excel\Concerns\ToModel;
		use Maatwebsite\Excel\Concerns\WithHeadingRow;

		class ClassName implements ToModel, WithHeadingRow
		{
		   
		    public function model(array $row)
		    {
			return new YourModel([
			    'name'     => $row['name'], 
			    'class'     => $row['class'], 
			    'roll'    => $row['roll'],
			]);
		    }
		}
		
Create Controller:

		php artisan make:controller YourController
		

In this step, now we should create new controller as MyController in this path "app/Http/Controllers/YourController.php". 
this controller will manage all importExportView, export and import request and return response, so put bellow content in controller file:

app/Http/Controllers/YourController.php

				<?php

				namespace App\Http\Controllers;

				use Illuminate\Http\Request;
				use App\Exports\UsersExport;
				use App\Imports\UsersImport;
				use Maatwebsite\Excel\Facades\Excel;

				class YourController extends Controller
				{
				   
				    public function importExportView()
				    {
				       return view('import');
				    }

				    public function import() 
				    {
					Excel::import(new ClassName,request()->file('file'));
					return back();
				    }
				}

If You want to upload a csv file with custom field (Text, select, number,Radio) value Then, You can try this Code:

				<?php

				namespace App\Http\Controllers;

				use Illuminate\Http\Request;
				use App\Imports\ClassName; 
				use Maatwebsite\Excel\Facades\Excel;

				class YourController extends Controller
				{
				   
				    public function importExportView()
				    {
				       return view('import');
				    }

				    public function import(Request $request)
				    {
					$data=array();
					$text=$request->your_field_name; //from input file name
					$number=$request->your_field_name;
					$status=$request->your_field_name;
				

					$rows = Excel::toArray(new ClassName, $request->file('file'));
					foreach ($rows as $value)
					{
						    foreach($value as $arr)
						    { 

						//value from custom input field

							$data['text']=$text;
							$data['exam_id']=$number;
							$data['status']=$status;

						//Value Receive from excel sheet
							$data['name']=$arr['name'];
							$data['class']=$arr['class'];
							$data['roll']=$arr['roll'];

							YourModel::create($data);
						    }

					}
					$notify[] = ['success', 'Data Imported Successfully.'];
					return back()->withNotify($notify);

				    }

				}
				
Blade file: resources/views/import.blade.php

			<!DOCTYPE html>
			<html>
			<head>
			    <title>Laravel Import Excel to database</title>
			    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.1.3/css/bootstrap.min.css" />
			</head>
			<body>

			<div class="container">
			    <div class="card bg-light mt-3">
				<div class="card-header">
				    Laravel 5.8 Import Export Excel to database Example - ItSolutionStuff.com
				</div>
				<div class="card-body">
				    <form action="{{ route('import') }}" method="POST" enctype="multipart/form-data">
					@csrf

					<input type="text" name="name" class="form-control">
					<input type="text" name="class" class="form-control">

					<input type="file" name="file" class="form-control">

					 <select class="form-control" id="sel1" name="status">
					    <option value="1">Active</option>
					    <option value="2">Inactive</option>		  
					  </select>

					<br>
					<button class="btn btn-success">Import User Data</button>
					<a class="btn btn-warning" href="{{ route('export') }}">Export User Data</a>
				    </form>
				</div>
			    </div>
			</div>

			</body>
			</html>
		
		
Good Luck ;)
Happy Coding :P
