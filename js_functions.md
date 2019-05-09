
Функция для расширений google chrome позволяющая записывать статус кнопки в storage. Необходимо, например, для сохранения и считывания настроек.

```
function setStatusInStorage(element_id) {
    chrome.storage.local.get({status: 0}, function (result) {
        var status = result.status;
        var property = document.getElementById(element_id);
        if (status == 0) {
            property.style.backgroundColor = "#4f8ff7"
            property.innerHTML = "<span>Enable control</span>"
            status = 1;
        }
        else {
            property.style.backgroundColor = "#a84237"
            property.innerHTML = "<span>Disable control</span>"
            status = 0;
        }
        chrome.storage.local.set({status: status});
    });
}
```
