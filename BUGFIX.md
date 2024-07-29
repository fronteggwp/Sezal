## Выявление  ошибки 
 Столкнувшись с ошибкой, при попытке отображения представления Few columns рассмотрим лог ошибки:
			`System.InvalidOperationException --> getGrouping: Sezal.Extensions.XData.XDataException: XData query execution failed,Query: SELECT *,FROM MainDemo.Module.BusinessObjects.Employee,QueryData: 
``
# Отладка ошибки
1. Перейдём в консоль разработчика нажав F12
2. Перейдём в раздел Preview, после чего увидим необходимую для нас информацию:
		**message:**``["getGrouping: Sezal.Extensions.XData.XDataException: XData query execution failed", "Query: SELECT *",…]``
		**stacktrace:**``[" at Sezal.Xum.SezalXumObjectSpaceProvider.GetDataAsync(GetDataContext context)",…]``
3. Проанализировав ошибку, мы понимаем что ошибка связана с некорректной группировкой данных 
4. Откроем серверную часть и проведём отладку используя точки останова на ключевых местах, а именно: 
			***Department.cs***
			***Empployee.cs***
5. При отладке ключевых мест мы увидим, что ошибка возникает в данном месте:
			`public virtual IList<Employee> Employees { get; set; } = new ObservableCollection<Employee>();`

# Временная изоляция ошибки 
1. Для изолирования ошибки перейдём в файл Model.xml и найдем представление Few columns
2. Найдя представление изучим *Id="Employee_ListView"*
3. Увидим код, описывающий Few columns:
		```
		<ListView Id="Employee_ListView" Caption="Employees" IsGroupPanelVisible="True" AutoExpandAllGroups="True" >`
		  `<Columns>`
		    `<ColumnInfo Id="Anniversary" PropertyName="Anniversary" Index="-1" />`
		    `<ColumnInfo Id="Department" PropertyName="Department" Index="-1" GroupIndex="0" />)`
		    `<ColumnInfo Id="FullName" Index="-1" />`
		    `<ColumnInfo Id="Manager" PropertyName="Manager" Index="-1" />`
		    `<ColumnInfo Id="NickName" PropertyName="NickName" Index="-1" />`
		    `<ColumnInfo Id="SpouseName" PropertyName="SpouseName" Index="-1" />`
		    `<ColumnInfo Id="WebPageAddress" PropertyName="WebPageAddress" Index="-1" />`
		    `<ColumnInfo Id="TitleOfCourtesy" PropertyName="TitleOfCourtesy" Index="0" />`
		    `<ColumnInfo Id="FirstName" PropertyName="FirstName" Index="2" IsNewNode="True" />`
		    `<ColumnInfo Id="LastName" PropertyName="LastName" Index="3" SortIndex="0" SortOrder="Ascending" Width="100" IsNewNode="True" />`
		    `<ColumnInfo Id="Position" Index="4" SortIndex="-1" SortOrder="None" />`
		    `<ColumnInfo Id="Email" Index="5" />`
		    `<ColumnInfo Id="Birthday" Index="6" />`
		  `</Columns>`
		  `<Filters CurrentFilterId="AllEmployees" IsNewNode="True">`
		    `<Filter Id="AllEmployees" Caption="All Employees" IsNewNode="True" />`
		    `<Filter Id="Managers" Criteria="Contains([Position.Title], 'Manager')" IsNewNode="True" />`
		    `<Filter Id="TopExecutives" Caption="Top Executives" Criteria="Contains([Position.Title], 'President') Or Contains([Position.Title], 'Director') Or StartsWith([Position.Title], 'Chief') And EndsWith([Position.Title], 'Officer')" IsNewNode="True" />`
		    `<Filter Id="Developers" Criteria="Position.Title = 'Developer'" IsNewNode="True" />`
		  `</Filters>`
		`</ListView>`
		```
5. Изолировать ошибку можно путем редактирования:
		`<ColumnInfo Id="Department" PropertyName="Department" Index="-1" GroupIndex="0" />)`
		Необходимо убрать: *GroupIndex="0"*
	На выходе получаем: 
		`<ColumnInfo Id="Department" PropertyName="Department" Index="-1"/>)`
