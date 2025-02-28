<template>
     <Header></Header>
     <div class="prof">
        <div class="prof_title">
            Здравствуйте, (имя пользователя!)
        </div>
        <div class="prof_info">
            <!-- Сделай вывод если заполнены, а если нет, чтоб предложили заполнить -->
            <div class="prof_info_title"> 
                Ваши данные
            </div>
            <div class="prof_info_block">
                Контактное лицо: <br>
                Адрес:<br>
                Телефон:<br>
                Реквизиты:<br>
                Имя компаниии:<br>
            </div>
            <div class="prof_info_i">
                Изменить
            </div>
        </div>
        <div class="prof_dok">
            <div class="prof_dok_title">
                Ваши текущие договоры
            </div>
            <div class="prof_dok_block">
                <table>
                    <thead>
                    <tr>
                        <th>Номер договора</th>
                        <th>Торговая точка</th>
                        <th>Имя клиента</th>
                        <th>Период аренды</th>
                        <th>Ежемесячная плата</th>
                        <th>Разорвать договор</th>
                    </tr>
                    </thead>
                </table>
            </div>
        </div>
     </div>
</template>