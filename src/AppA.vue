<script lang="coffee">

import { reactive, watch, ref, computed } from 'vue'
import Block from '@/components/Block.vue'
import Btn from '@/components/Btn.vue'
import ModalResult from '@/components/Modal.vue'
import data from '@/data/a.coffee'
import results from '@/data/result.coffee'

export default
    name: 'AppA'
    components: {
        Block
        Btn
        ModalResult
    }
    # props:
    # emits:

    setup: (props, context) ->
        currentBlock = ref 0
        # currentBlock = ref 1
        nextBlock = 1
        blocks = reactive data()
        isAgeError = ref no
        isOpenResult = ref no
        isResult = ref no
        result = ref {}
        doseGrow = 0

        watchFields = ->
            {
                A1, B1, C1, D1, E1, F1, G1, H1 # Общие вопросы
                A2, B2, C2, D2, E2, F2, G2, H2, I2 # Гипотиреоз
                A3, B3, C3, D3, E3 # Беременность
                A4, B4, C4, D4, E4, F4, G4, H4 # ВДРЩЖ

            } = blockItems()

            A1: A1.field.val
            B1: B1.field.val
            C1: C1.field.val
            D1: D1.field.val
            E1: E1.field.val
            F1: F1.field.val
            G1: G1.field.val
            H1: H1.field.val
            
            A3: A3.field.val
            B3: B3.field.val
            C3: C3.field.val
            D3: D3.field.val
            E3: E3.field.val

            A2: A2.field.val
            B2: B2.field.val
            C2: C2.field.val
            D2: D2.field.val
            E2: E2.field.val
            F2: F2.field.val
            G2: G2.field.val
            H2: H2.field.val
            I2: I2.field.val

            A4: A4.field.val
            B4: B4.field.val
            C4: C4.field.val
            D4: D4.field.val
            E4: E4.field.val
            F4: F4.field.val
            G4: G4.field.val
            H4: H4.field.val

        blockItems = -> {
            # 0 Общие вопросы
            A1: blocks[0].items[0]# Возраст
            B1: blocks[0].items[1]# Пол
            C1: blocks[0].items[2]# Беременность
            D1: blocks[0].items[3]# Причина гипотиреоза
            E1: blocks[0].items[4]# Операция была выполнена по поводу
            F1: blocks[0].items[5]# Вес
            G1: blocks[0].items[6]# Рост
            H1: blocks[0].items[7]# ИМТ (Индекс массы тела)

            # 1 Гипотиреоз
            A2: blocks[1].items[0]# Уровень ТТГ
            B2: blocks[1].items[1]# Уровень Т4св
            C2: blocks[1].items[2]# Титр АТ-ТПО
            D2: blocks[1].items[3]# Наличие сердечно-сосудистого заболевания
            E2: blocks[1].items[4]# Симптомы и признаки гипотиреоза
            F2: blocks[1].items[5]# Наличие факторов риска ССЗ
            G2: blocks[1].items[6]# Наличие специфических факторов риска
            H2: blocks[1].items[7]# Есть факторы риска вторичного гипотиреоза
            I2: blocks[1].items[8]# Планирование беременности
            # J2: blocks[1].items[9]# Планирование беременности
            # K2: blocks[1].items[10]# Есть фаткоры риска вторичного гипотиеоза

            # 2 Беременность
            A3: blocks[2].items[0]# Прием левотироксина до наступления беременности
            B3: blocks[2].items[1]# Введите текущую дозу левотироксина
            C3: blocks[2].items[2]# Введите уровень ТТГ
            D3: blocks[2].items[3]# Введите уровень Т4св
            E3: blocks[2].items[4]# Титр АТ-ТПО

            # 3 ВДРЩЖ
            A4: blocks[3].items[0]# Группа риска
            B4: blocks[3].items[1]# Тип вмешательства
            C4: blocks[3].items[2]# Уровень ТГ
            D4: blocks[3].items[3]# Проводилась ли аблация остаточной тиреоидной ткани
            E4: blocks[3].items[4]# Постменопауза
            F4: blocks[3].items[5]# Остеопороз
            G4: blocks[3].items[6]# Фибрилляция предсердий
            H4: blocks[3].items[7]# Тахикардия
        }

        calculate = () ->
            {
                A1, B1, C1, D1, E1, F1, G1, H1
                A2, B2, C2, D2, E2, F2, G2, H2, I2, J2, K2
                A3, B3, C3, D3, E3
                A4, B4, C4, D4, E4, F4, G4, H4

            } = blockItems()

            # F1val = Number F1.field.val
            A1val = Number A1.field.val
            B1val = Number B1.field.val


            isAgeError.value = 0 < A1val < 18
            isResult.value = no
            result.value = results().zero
            # result.value = results(doseGrow * 0.9).C1

            # 0 Общие вопросы
            if currentBlock.value is 0
                F1val = Number F1.field.val
                G1val = Number G1.field.val
                C1val = Number C1.field.val
                nextBlock = 1
                C1.active = B1val is 2
                if C1.active and C1val is 1
                    nextBlock = 2
                D1.active = no
                E1.active = no
                if (C1.active and C1val is 0) or B1val is 1
                    D1.active = yes
                    if D1.field.val is 1
                        E1.active = yes
                        if E1.field.val is 1
                            nextBlock = 3
                # Индекс массы тела
                h = G1val / 100
                I = ''
                if F1val > 0 and h > 0
                    I = F1val / (h * h)
                    I = Math.round(I * 10) / 10
                    I = 100 if I > 100
                    I = 0 if I < 0
                doseGrow = F1val
                if I > 30
                    doseGrow = G1val - 100
                    doseGrow = doseGrow * .9 if B1val is 2
                H1.field.val = I
                return yes

            # 1 Гипотиреоз
            if currentBlock.value is 1
                isResult.value = yes
                A2_val = Number String(A2.field.val).replace ',', '.'
                B2_val = B2.field.val


                B2.field.options[2].disabled = no
                C2.field.options[0].disabled = yes
                C2.active = no
                D2.active = no
                E2.active = no
                F2.active = no
                G2.active = no
                H2.active = no
                I2.active = no

                # A2_val = .3
                # B2_val = 2
                # A1.field.val = 60
                # B1.field.val = 2
                # D2.field.val = 1

                if 0 <= A2_val < .4
                    if B2_val is 1
                        D2.active = yes
                        E2.active = yes
                        F2.active = yes
                        G2.active = yes
                        H2.active = yes
                        E2yes = E2.field.val is 1
                        F2yes = F2.field.val is 1
                        G2yes = G2.field.val is 1
                        H2yes = H2.field.val is 1
                        I2yes = no
                        result.value = results().D1
                        if 56 > A1val
                            if B1val is 2
                                I2.active = yes
                                I2yes = I2.field.val is 1
                            if D2.field.val is 1
                                if E2yes or F2yes or G2yes or H2yes or I2yes
                                    result.value = results(doseGrow * 0.9).C1
                            if D2.field.val is 0
                                if E2yes or F2yes or G2yes or H2yes or I2yes
                                    result.value = results(doseGrow * 1.6).E1
                        if 55 < A1val
                            if D2.field.val is 1
                                if E2yes or F2yes or G2yes or H2yes or I2yes
                                    result.value = results(doseGrow * 0.9).F1
                            if D2.field.val is 0
                                if E2yes or F2yes or G2yes or H2yes or I2yes
                                    result.value = results(doseGrow * 0.9).C1
                    if B2_val is 2
                        result.value = results().A1
                    if B2_val is 3
                        result.value = results().B1

                # A1.field.val = 60
                # B1.field.val = 2
                # A2_val = 3
                # B2_val = 1
                # H2.field.val = 0
                # D2.field.val = 1
                # C2.field.val = 2
                # E2.field.val = 1

                if .4 <= A2_val < 4
                    B2.field.options[2].disabled = yes
                    C2.active = yes
                    if B2_val is 1
                        D2.active = yes
                        E2.active = yes
                        F2.active = yes
                        G2.active = yes
                        H2.active = yes
                        E2yes = E2.field.val is 1
                        F2yes = F2.field.val is 1
                        G2yes = G2.field.val is 1
                        H2yes = H2.field.val is 1
                        I2yes = no
                        if B1val is 2
                            I2.active = yes
                            I2yes = I2.field.val is 1
                        if H2.field.val is 1
                            if 56 > A1val
                                if D2.field.val is 1
                                    result.value = results(doseGrow * 0.9).G1
                                if D2.field.val is 0
                                    result.value = results(doseGrow * 1.6).K1
                            if 55 < A1val
                                if D2.field.val is 1
                                    result.value = results(doseGrow * 0.9).G1
                                if D2.field.val is 0
                                    result.value = results(doseGrow * 0.9).H1
                        if H2.field.val is 0
                            if C2.field.val is 1
                                result.value = results().L1
                            if (C2.field.val is 2) or (C2.field.val is 3)
                                result.value = results().M1
                            if E2yes or F2yes or G2yes or I2yes
                                result.value = results().N1
                    if B2_val is 2
                        if C2.field.val is 1
                            result.value = results().I1
                        if (C2.field.val is 2) or (C2.field.val is 3)
                            result.value = results().J1

                # A1.field.val = 60
                # B1.field.val = 2
                # F1.field.val = 80
                # A2_val = 5
                # B2_val = 2
                # # H2.field.val = 0
                # D2.field.val = 0
                # C2.field.val = 3
                # # E2.field.val = 1

                if 4 <= A2_val < 10
                    B2.field.options[2].disabled = yes
                    if B2_val is 1
                        D2.active = yes
                        if 56 > A1val
                            if D2.field.val is 1
                                result.value = results(doseGrow * 0.9).O1
                            if D2.field.val is 0
                                result.value = results(doseGrow * 1.6).P1
                        if 55 < A1val
                            if D2.field.val is 1
                                result.value = results(doseGrow * 0.9).O1
                            if D2.field.val is 0
                                result.value = results(doseGrow * 0.9).Q1
                    if B2_val is 2
                        C2.active = yes
                        D2.active = yes
                        E2.active = yes
                        F2.active = yes
                        G2.active = yes
                        E2isYes = E2.field.val is 1
                        F2isYes = F2.field.val is 1
                        G2isYes = G2.field.val is 1
                        I2isYes = no
                        if C2.field.val is 1
                            if 56 > A1val
                                if B1val is 2
                                    I2.active = yes
                                    I2isYes = I2.field.val is 1
                                if D2.field.val is 1
                                    result.value = results(doseGrow * 0.9).R1
                                    if I2isYes
                                        result.value = results(doseGrow * 0.9).S1
                                if D2.field.val is 0
                                    result.value = results(doseGrow).T1
                                    if I2isYes
                                        result.value = results(doseGrow).U1
                            if 55 < A1val
                                result.value = results(doseGrow * 0.9).R1
                        if C2.field.val is 2
                            if 56 > A1val
                                if B1val is 2
                                    I2.active = yes
                                    I2isYes = I2.field.val is 1
                                if D2.field.val is 1
                                    result.value = results(doseGrow * 0.9).R1
                                    if I2isYes
                                        result.value = results(doseGrow * 0.9).S1
                                if D2.field.val is 0
                                    result.value = results().V1
                                    if E2isYes or F2isYes or G2isYes
                                        result.value = results(doseGrow).T1
                                    if I2isYes
                                        result.value = results(doseGrow).U1
                            if 55 < A1val
                                result.value = results(doseGrow * 0.9).R1
                                if D2.field.val is 0
                                    if not E2isYes and not F2isYes and not G2isYes
                                        result.value = results().V1
                        if C2.field.val is 3
                            if 56 > A1val
                                if B1val is 2
                                    I2.active = yes
                                    I2isYes = I2.field.val is 1
                                if D2.field.val is 1
                                    result.value = results(doseGrow * 0.9).R1
                                    if I2isYes
                                        result.value = results(doseGrow * 0.9).S1
                                if D2.field.val is 0
                                    result.value = results().W1
                                    if E2isYes or F2isYes or G2isYes
                                        result.value = results(doseGrow).T1
                                    if I2isYes
                                        result.value = results(doseGrow).U1
                            if 55 < A1val
                                result.value = results(doseGrow * 0.9).R1
                                if D2.field.val is 0
                                    if not E2isYes and not F2isYes and not G2isYes
                                        result.value = results().W1

                # A1.field.val = 60
                # B1.field.val = 2
                # F1.field.val = 80
                # A2_val = 10
                # B2_val = 2
                # H2.field.val = 0
                # D2.field.val = 1
                # C2.field.val = 3
                # E2.field.val = 1

                if 10 <= A2_val
                    B2.field.options[2].disabled = yes
                    D2.active = yes
                    C2.active = yes
                    if B2_val is 1
                        if 56 > A1val
                            result.value = results(doseGrow * 1.6).P1
                            if D2.field.val is 1
                                result.value = results(doseGrow * 0.9).O1
                                
                        if 55 < A1val
                            result.value = results(doseGrow * 0.9).Q1
                            if D2.field.val is 1
                                result.value = results(doseGrow * 0.9).O1
                    if B2_val is 2
                        if 56 > A1val
                            E2.active = yes
                            E2isYes = E2.field.val is 1
                            I2isYes = no
                            if B1val is 2
                                I2.active = yes
                                I2isYes = I2.field.val is 1
                            if D2.field.val is 1
                                result.value = results(doseGrow * 0.9).X1
                                if I2isYes
                                    result.value = results(doseGrow * 0.9).S1
                            if D2.field.val is 0
                                result.value = results().Y1
                                if E2isYes
                                    result.value = results(doseGrow).Z1
                                if I2isYes
                                    result.value = results(doseGrow).U1
                        if 55 < A1val
                            result.value = results(doseGrow * 0.9).AA1
                            if D2.field.val is 1
                                result.value = results(doseGrow * 0.9).X1

                return yes

            # 2 Беременность
            if currentBlock.value is 2
                isResult.value = yes
                A3yes = A3.field.val is 1
                A3no = A3.field.val is 0
                B3.active = A3yes
                C3.active = A3no
                D3.active = A3no
                E3.active = A3no
                if A3yes
                    result.value = results(Number(B3.field.val) * 1.3).A2
                    return yes
                if A3no
                    isD3_1 = D3.field.val is 1
                    isD3_2 = D3.field.val is 2
                    isE3_1 = E3.field.val is 1
                    isE3_2 = E3.field.val is 2
                    C3val = Number String(C3.field.val).replace ',', '.'
                    if 0 <= C3val < 2.5
                        if isD3_1 and isE3_1
                            result.value = results().B2
                            return yes
                        if isD3_1 and isE3_2
                            result.value = results().C2
                            return yes
                        if isD3_2 and isE3_1
                            result.value = results().D2
                            return yes
                        if isD3_2 and isE3_2
                            result.value = results().E2
                            return yes
                    if 2.5 <= C3val < 4
                        if (isD3_1 and isE3_1) or (isD3_1 and isE3_2)
                            result.value = results(doseGrow * 2.3).F2
                            return yes
                        if isD3_2 and isE3_1
                            result.value = results(doseGrow * 1.2).G2
                            return yes
                        if isD3_2 and isE3_2
                            result.value = results().H2
                            return yes
                    if 4 <= C3val < 10
                        if (isD3_1 and isE3_1) or (isD3_1 and isE3_2)
                            result.value = results(doseGrow * 2.3).F2
                            return yes
                        if isD3_2 and isE3_1
                            result.value = results(doseGrow * 1.2).I2
                            return yes
                        if isD3_2 and isE3_2
                            result.value = results(doseGrow * 1.2).G2
                            return yes
                    if 10 <= C3val
                        if (isD3_1 and isE3_1) or (isD3_1 and isE3_2)
                            result.value = results(doseGrow * 2.3).F2
                            return yes
                        if (isD3_2 and isE3_1) or (isD3_2 and isE3_2)
                            result.value = results(doseGrow * 2.3).J2
                            return yes
                return yes

            # 3 ВДРЩЖ
            if currentBlock.value is 3
                isResult.value = yes
                E4.active = no
                F4.active = no
                G4.active = no
                H4.active = no
                C4.active = no
                D4.active = no
                A4val = A4.field.val
                B4val = B4.field.val
                C4val = C4.field.val
                B4.field.options[0].disabled = no
                B4.field.options[1].disabled = no
                if A4val is 1
                    if B4val is 1
                        result.value = results().A3
                    if B4val is 2
                        C4.active = yes
                        if C4val is 1
                            D4.active = yes
                            result.value = results(doseGrow * 1.6).D3
                        if C4val is 2 or C4val is 3
                            F4.active = yes
                            G4.active = yes
                            H4.active = yes
                            E4.active = B1val is 2
                            F4yes = F4.field.val is 1
                            G4yes = G4.field.val is 1
                            H4yes = H4.field.val is 1
                            E4yes = E4.field.val is 1
                            result.value = results(doseGrow * 2.2).B3
                            if F4yes or G4yes or H4yes or E4yes or (A1val > 60)
                                result.value = results(doseGrow * 1.6).D3

                if A4val is 2
                    B4.field.options[0].disabled = yes
                    B4.field.val = B4.field.options[1].val
                    F4.active = yes
                    G4.active = yes
                    H4.active = yes
                    F4yes = F4.field.val is 1
                    G4yes = G4.field.val is 1
                    H4yes = H4.field.val is 1
                    E4yes = no
                    result.value = results(doseGrow * 2.2).C3
                    if B1val is 2
                        E4.active = yes
                        E4yes = E4.field.val is 1
                    if H4yes or E4yes
                        result.value = results(doseGrow * 2.2).B3
                    if F4yes or G4yes or (A1val > 60)
                        result.value = results(doseGrow * 1.6).D3

                if A4val is 3
                    B4.field.options[0].disabled = yes
                    B4.field.val = B4.field.options[1].val
                    G4.active = yes
                    result.value = results(doseGrow * 2.2).C3
                    if G4.field.val is 1
                        result.value = results(doseGrow * 2.2).B3
                return yes

            yes

        watch watchFields, calculate

        onShowResult = () ->
            return yes unless blocks[currentBlock.value].isComplete
            isOpenResult.value = true
            yes

        onResetData = () ->
            window.location.reload()
            # blocks[index] = dataDefault for dataDefault, index in data()
            yes

        onGoHome = () ->
            window.location.href = '/'
            yes

        # onReferences = () ->
        #     currentUrl = new URL document.URL
        #     currentUrl.hash = '#references'
        #     window.location.href = currentUrl.href
        #     yes

        onNavigate = (step) ->
            if step < 0
                currentBlock.value = 0
            if step > 0
                currentBlock.value = nextBlock
            calculate()
            yes

        calculate()

        {
            currentBlock
            blocks
            onResetData
            onGoHome
            # onReferences
            onNavigate
            isAgeError
            isOpenResult
            isResult
            onShowResult
            result
        }

</script>

<template>

<div class="c-wrapper">
    <block
        :model="blocks[currentBlock]"
        :is-result="isResult"
        :is-age-error="isAgeError"
        @navigate="onNavigate"
        @show-result="onShowResult"
    />

    <modal-result
        :open-modal="isOpenResult"
        wrapper-class="c-modal-result"
        @closed="isOpenResult = false"
    >
        <div class="row g-10">
            <div class="col-100 col-lg">
                <div class="c-result-body maxw-lg-5">
                    <div class="c-result-label">
                        Результат расчета
                    </div>
                    <div class="c-result-title" v-html="result.title"></div>
                    <div class="c-result-text" v-html="result.body"></div>
                    <div class="row g-5 mt-lg-35">
                        <div class="col-100 col-md">
                            <btn class="c-btn-red" @click="onResetData">
                                Рассчитать заново
                            </btn>
                        </div><!-- .col -->
                        <div class="col-100 col-md">
                            <btn class="c-btn-lgray" @click="onGoHome">
                                На главную
                            </btn>
                        </div><!-- .col -->
                    </div><!-- .row -->
                </div>
            </div><!-- .col -->
            <div class="col-100 col-lg-auto ms-auto align-self-end">
                <div class="maxw-lg-4">
                    <div v-if="result.info">
                        <div class="c-result-info">
                            <img class="c-result-info-icon" width="23" height="23" src="@/img/i-info.svg">
                            <div v-html="result.info"></div>
                        </div>
                        <a class="c-btn c-btn-primary" aria-haspopup="dialog" href="#references">
                            Список литературы
                        </a>
                    </div><!-- v-if -->
                    <div v-else>&nbsp;</div>
                </div>
            </div><!-- .col -->
        </div><!-- .row -->
    </modal-result>

</div>

</template>