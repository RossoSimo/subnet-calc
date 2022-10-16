<template>
    <div class="mx-sm-3">
        <h1>Calcolatore subnet</h1>
        <form @submit.prevent="handleSubmit(ip, mask)">
            <div class="form-row">
                <div class="form-group col-md-5 mb-3 w-100">
                    <label for="ip">Indirizzo IP</label>
                    <input class="form-control " type="text" name="ip" id="ip" v-model="ip" placeholder="Es. 192.168.1.1">
                </div>
                <div class="form-group col-md-5 mb-3 w-100">
                    <label for="mask">Maschera</label>
                    <input class="form-control" type="number" name="mask" id="mask" v-model="mask" placeholder="Es. 27">
                </div>
                <input class="btn btn-primary me-2" type="submit" value="Invio">
                <button v-if="submitted" type="button" class="btn btn-secondary" @click="toBinary()">{{ convert }}</button>
            </div>
        </form>

        <div v-if="submitted && binary == false">
            <div>
                <h3>Network IP</h3>
                <p>{{ net }}</p>
                <h3>Subnet</h3>
                <p>{{ subnet }}</p>
                <h3>Ip broadcast</h3>
                <p>{{ ip_broad }}</p>
                <h3>Numero host</h3>
                <p>{{ host_numb }}</p>
                <h3>Primo Ip </h3>
                <p>{{ first }}</p>
                <h3>Ultimo ip</h3>
                <p>{{ last }}</p>
            </div>
        </div>
        <div v-if="submitted && binary">
            <div>
                <h3>Network IP</h3>
                <p>{{ b_net }}</p>
                <h3>Subnet</h3>
                <p>{{ b_subnet }}</p>
                <h3>Ip broadcast</h3>
                <p>{{ b_ip_broad }}</p>
                <h3>Numero host</h3>
                <p>{{ host_numb }}</p>
                <h3>Primo Ip </h3>
                <p>{{ b_first }}</p>
                <h3>Ultimo ip</h3>
                <p>{{ b_last }}</p>
            </div>
        </div>
    </div>
</template>

<script>
const axios = require("axios").default;

export default {
    name: 'App',
    data () {
        return {
            convert: "In binario",
            submitted: false,
            binary: false,
        
            net: 0,
            subnet: 0,
            ip_broad: 0,
            host_numb: 0,
            first: 0,
            last: 0,

            b_net: '',
            b_subnet: '',
            b_ip_broad: '',
            b_first: '',
            b_last: ''
        }
    },

    methods: {
        toBinary() {
            if(!this.binary) {
                this.convert = "In decimale";
                this.binary = true;
            } else {
                this.convert = "In binario";
                this.binary = false;
            }
        },
        validateIPaddress(ip) {
            if (/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ip)) {
                return true;
            } else {
                return false;
            }
        },

        validateMask(mask) {
            if (mask >= 8 && mask <= 32) {
                return true;
            } else {
                return false;
            }
        },

        handleSubmit(ip, mask) {
            console.log(ip);
            console.log(mask);
            let t = this;
            if (this.validateIPaddress(ip) && this.validateMask(mask)) {
                this.submitted = true;
                axios.get(`https://networkcalc.com/api/ip/${ip}/${mask}?binary=true`)
                .then(function (res) {
                    if(res.status == 200) {
                        let addr = res.data.address;
                        console.log(addr);
                        
                        t.net = addr.network_address;
                        t.subnet = addr.subnet_mask;
                        t.ip_broad = addr.broadcast_address;
                        t.host_numb = addr.assignable_hosts;
                        t.first = addr.first_assignable_host;
                        t.last = addr.last_assignable_host;
                        
                        t.b_net = addr.binary.network_address
                        t.b_subnet = addr.binary.subnet_mask
                        t.b_ip_broad = addr.binary.broadcast_address
                        t.b_first = addr.binary.first_assignable_host
                        t.b_last = addr.binary.last_assignable_host
                    } 
                })
                .catch(err => console.log(err))
            } else {
                alert("L'indirizzo IP o la maschera di sottorete inseriti sono sbagliati.");
            }
        }
    }
}

</script>

<style>
html, body {
    height: 100%;
}

html {
    display: table;
    margin: auto;
}

body {
    display: table-cell;
    vertical-align: middle;
}
</style>