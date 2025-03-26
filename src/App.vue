<template>
  <div class="container">
    <h1>WiFi QR Code Generator</h1>
    <div class="form">
      <div class="form-group">
        <label for="ssid">WLAN Name (SSID):</label>
        <input type="text" id="ssid" v-model="ssid" placeholder="WLAN name">
      </div>
      
      <div class="form-group">
        <label for="password">WLAN Password:</label>
        <input type="password" id="password" v-model="password" placeholder="WLAN password">
      </div>
      
      <div class="form-group">
        <label for="encryption">Encryption Type:</label>
        <select id="encryption" v-model="encryption">
          <option value="WPA">WPA/WPA2</option>
          <option value="WEP">WEP</option>
          <option value="nopass">No Encryption</option>
        </select>
      </div>

      <div class="form-group">
        <label for="hidden">
          <input type="checkbox" id="hidden" v-model="hidden"> Hide SSID (Not broadcast)
        </label>
      </div>
    </div>

    <div class="qrcode" v-if="display">
      <canvas ref="qrcode"></canvas>
    </div>
  </div>
</template>

<script>
import QRCode from 'qrcode'
import { ref, watch, onMounted } from 'vue'

export default {
  name: 'App',
  setup() {
    const ssid = ref('')
    const password = ref('')
    const encryption = ref('WPA')
    const qrcode = ref(null)
    const display = ref(false)
    const hidden = ref(false)

    const generateQRCode = async () => {
      if (!ssid.value) return

      const wifiString = `WIFI:T:${encryption.value};S:${ssid.value};P:${password.value};H:${hidden.value ? 'true' : 'false'};;`
      
      try {
        await QRCode.toCanvas(qrcode.value, wifiString, {
          width: 250,
          margin: 2,
          color: {
            dark: '#000000',
            light: '#ffffff'
          }
        })
      } catch (err) {
        console.error(err)
      }
    }

    watch([ssid, password, encryption, hidden], () => {
      generateQRCode()
      display.value = true
    })

    onMounted(() => {
      generateQRCode()
    })

    return {
      ssid,
      password,
      encryption,
      qrcode,
      display,
      hidden
    }
  }
}
</script>

<style>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
}

.form {
  margin-bottom: 30px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  color: #666;
}

.form-group input[type="checkbox"] {
  margin-right: 8px;
  width: auto;
}

input, select {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.qrcode {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

canvas {
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 10px;
}

</style>
