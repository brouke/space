<template>
    <Header></Header>
    <div class="blck">
        <div class="blck_title">Помещения</div>

        <div class="blck_all">
        <div class="blck_all_add">
            <div class="blck_all_add_title">Добавить помещения</div>
            
        </div>

        <div class="blck_all_room">
            <div class="blck_all_room_title">Все помещения</div>
            <div class="blck_all_room_one">
            <div class="blck_all_room_one_img">
            </div>

            <div class="blck_all_room_one_info">
                <div class="block_all_room_one_info_title">Помещение А 70 000 Р.</div>
                        <div class="block_all_room_one_info_desc">1 этаж, 100 кв. м.</div>
                            <div class="block_all_room_one_info_details">Аренда помещения на первом этаже торгового центра 
                                                            площадью 100 кв. метров с кондиционером. Идеально 
                                                            подходит для розничной торговли или офисов, с большими 
                                                            витринными окнами для привлечения клиентов. </div>
                        
                        <Button 
                        placeholder="Изменить"
                        :className="'btn_buy-button'"
                        />
            </div>
            </div>
        </div>
        </div>
    </div>
       
</template>