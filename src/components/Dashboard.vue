<template>
    <Message :msg="msg" v-show="msg"/>
    <div id="burger-table">
        <div>
            <div id="burger-table-heading">
                <div class="order-id">#:</div>
                <div>Cliente:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opcionais:</div>
                <div>Ações:</div>
            </div>
        </div>
    </div>

    <div id="burger-table-rows">
        <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
            <div class="order-number">{{ burger.id }}</div>
            <div>{{ burger.nome }}</div>
            <div>{{ burger.pao }}</div>
            <div>{{ burger.carne}}</div>
            <div>
                <ul v-for="(opcionais, index) in burger.opcionais" :key="index">
                    <li >{{ opcionais }}</li>
                </ul>
            </div>
            <div>
                <select name="status" class="status" @change="updateStatus($event, burger.id)">
                    <option value="">Status</option>
                    <option v-for="s in status" :key="s.id" :selected="burger.status == s.tipo" :value="s.tipo"> {{ s.tipo }}</option>
                </select>
                <button class="delete-btn" @click="deletePedido(burger.id)">Cancelar</button>
            </div>
        </div>
    </div>
</template>

<style scoped>
    #burger-table {
        max-width: 1200px;
        margin: 0 auto;
    }

    #burger-table-heading,
    #burger-table-rows,
    .burger-table-row {
        display: flex;
        flex-wrap: wrap;
    }

    #burger-table-heading {
        font-weight: bold;
        padding: 12px;
        border-bottom: 3px solid #333;
    }

    #burger-table-heading div,
    .burger-table-row div {
        width: 19%;
    } 

    .burger-table-row {
        width: 100%;
        padding: 12px;
        border: 1px solid #ccc;
    }

    #burger-table-heading .order-id,
    .burger-table-row .order-number {
        width: 5%;
    }

    select {
        padding: 12px 6px;
        margin-right: 12px;
    }

    .delete-btn {
        background-color: #222;
        color: #FCBA03;
        font-weight: bold;
        border: 2px solid #222;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;
        transition: .5s;
    }
    
    .delete-btn:hover {
        background-color: transparent;
        color: #222;
    }
</style>

<script>
    import Message from './Message.vue';

    export default {
        name: "Dashboard",
        data() {
            return {
                burgers: null,
                burger_id: null,
                status: [],
                msg: null
            }
        },
        components: {
            Message
        },
        methods: {
            async getPedidos() {
                const req = await fetch("http://localhost:3000/burgers");
                const data = await req.json();

                this.burgers = data;
                
                this.getStatus();
            },

            async getStatus() {
                const req = await fetch("http://localhost:3000/status");
                const data = await req.json();

                this.status = data;
                console.log(data)
            },
            async deletePedido(id) {
                const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                    method: "DELETE"
                });

                const res = await req.json();

                 // mensagem para o usuario
                 this.msg = `Pedido removido com sucesso!`;

                // apagar a mensagem
                setTimeout(() => this.msg = "", 3000);

                this.getPedidos();
            },
            async updateStatus(event, id) {
                const option = event.target.value;
                const dataJson = JSON.stringify({status: option});

                const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                    method: "PATCH",
                    headers: { "Content-Type": "application/json" },
                    body: dataJson
                });

                const res = await req.json();
                console.log(res)
            }
        },
        mounted() {
            this.getPedidos();
        }
    }

</script>