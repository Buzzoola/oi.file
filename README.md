oi.file � ��������� ������ ��� AngularJS
=======


## �������� �����������

* ����� ������ ����� ��������� � ���������������
* ���������
* �������� �������� �� �������� �� ������ (���� ������� ������������ FileReader)
* �������� ������ ����� xhr � iframe (��� ������ ���������)
* ������ � ������ ���������� � ������, �� ����� ���� �������� ��������
* ����� ���������� ������� POST �� ������ ������� �� ������ ���� 

[������������](http://tamtakoe.ru/uploader/), [���������](http://plnkr.co/edit/HKbvgle4zqfqCKcpLJDi?p=preview)

## ����������

����������� ������
```javascript
angular.module('myApp', ['oi.file']);
```

������������� ���������
```html
<!-- �������� ����� ��������� -->
<input type="file" oi-file="options">

<!-- �������� ��������������� �� ������� -->
<ul oi-file="options">
  <li ng-repeat="item in items">
    <img ng-src="{{item.thumb}}"> 
  </li>
</ul>
```
*������, ������������� ����� � �� ������ ������ ������*

��������� ���������� � �����������
```javascript
$scope.items = model //������� ��� ������ ���������, ������� ����� ��������� ��������� ������
$scope.options = {
  change: function (file) {
    //������� ������ ������� ��� �������� �����
    $scope.add(function (i, id) {
    
      file.$preview($scope.items[i]); //��������� �������� ����� FileReader �� ��������
      
      file.$upload('uploader.php' + id, $scope.items[i], {allowedType: ["jpeg", "jpg", "png", "gif"]})
        .catch(function (data) {
          //������� ������� ��� ��������� ��������
          $scope.del($scope.getIndexById(data.item.id));
        })
    })
  }
}```
*����� catch ��������, ������� � �������� 1.2. � ������ ������� ����������� ������ ���� then(null, function (data) {...})*

$preview � $upload ���������� ��������. ��. [$q](http://www.angular.ru/api/ng.$q)

������ � ����������� ����������� �� �������
```javascript
$scope.items = model //������� ��� ������ ���������, ������� ����� ��������� ��������� ������
$scope.options = {
  change: function (file) {
    //������� ������ ������� ��� �������� �����
    $scope.add(function (i, id) {
    
      file.$preview($scope.items[i])
        .then(function (data) {
          //����������� ���������. ��������� ��� � ������� canvas
          minimize(file._file);
          //����������
          file.$upload('uploader.php' + id, $scope.items[i])
          
        }, function (data) {
          //����������� �� ���������. ���������� ��� ����
          file.$upload('uploader.php' + id, $scope.items[i])
        });
    })
  }
}```

������ ���������� � ������ $upload ���� ������ � ����������� ���������.
������ �������� ����� ���������� ������� ���������, ������� ����� ��������������.
���������� ����� �������������� ������� ��������� ������.

�������� �� ��������� ����� �������������� � �������-���������� `oiFileConfig`

## ���
- **change** `function (file)`. ��������� ������� �����. ���� null � �� ��������������
    - **file** `{object}` - ������ �����, ���������� ���������� � ��������� ����� � ������:
       - $preview `function (item, [field])`     //item - ������, field - ���� � ����������, ������� ��������� � ������ (���� �� �������, ������� �� ���������)
                                                //���������� �������� � ���������: success, error
       - $upload `function (url, item, [permit])` //url - ������ ��������, item - ������ (����� ������� {}), permit - ������ ���������� ��������� (��. ����)
                                                //���������� �������� � ���������: success, error, notice

� ������� �������� ���������� xhr (��� �����, ��� �������� ����� iframe), ����������� ������:
item: {...}     //������, � ������� �������������� ��������
response: {...} //����� �������, ��������������� �� JSON

- **validate** `function (file, permit)`. ��������� ������
    - **file** `{object}`   - ������ �����
    - **permit** `{object}` - ��������� ��� ���������. ������:
        - allowedType: ["jpeg", "jpg", "png", "gif"], //������ ����������� ����������
        - maxNumberOfFiles: 100, //������������ ���������� ������
        - maxSize: 4194304,      //������������ ������ �����
        - maxSpace: 104857600,   //����������� ��������� ����� �� �������
        - quantity: 3,           //��������� ������
        - space: 481208,         //������ �����
        - errorBadType: "����� ���������: JPEG, JPG, PNG, GIF", //����� ������...
        - errorBigSize: "��� �� ����� 4 ��",
        - errorMaxQuantity: "��������� ������������ ���������� ������: 100",
        - errorMaxSize: "�������� 2,3 �� ���������� �����"
    - *return* `{object}` - ������ �������� ������ [{msg: '����� ������', code: '���'}, {...}, ... ]

- **setError** `function (code, data)`. ��������� ������
    - **code** `{string}` - ��� ������
    - **data** `{object}` - xhr (��� �����, ��� �������� ����� iframe), ����������� ������:
        - item: {...}     //������, � ������� �������������� ��������
        - response: {...} //����� �������, ��������������� �� JSON

- **url** `{string}`.          ���� �� ��������� �� ������� ��������
- **fieldName** `{string}`.    ���� � ������� $_FILES - 'Files'
- **fileClass** `{string}`.    ��� ������, ���� ��������������� ���� - 'dragover-file'
- **notFileClass** `{string}`. ��� ������, ���� ��������������� �� ���� - 'dragover-plain'

����� �����, ����������� � ������
- **fileName** `{string}`.     ��� ����� - 'filename'
- **fileThumb** `{string}`.    ������ �� ��������� - 'thumb',
- **fileSize** `{string}`.     ������ ����� - 'size',
- **fileProgress** `{string}`. ������� �������� (� ����� ��� ���� ��������) - 'progress'