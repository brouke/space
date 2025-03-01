<template>
    <Header></Header>

    <div class="prod">
        <div class="prod_info">
            <div class="prod_info_img"></div>
            <div class="prod_info_desc">
                <div class="prod_info_desc_title">Имя + Cтоимость</div>
                <div class="prod_info_desc_inf">Описание</div>
                <div class="prod_info_desc_stat">
                    <li>Площадь:</li>
                    <li>Этаж:</li>
                    <li>Кондиционер:</li>
                    <li>Стоимость:</li>
                </div>
                <Button 
                        placeholder="Арендовать"
                        :className="'btn_buy-button'"
                        />
            </div>
        </div>
    </div>
</template>