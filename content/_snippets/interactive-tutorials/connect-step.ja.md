{% if use_network is undefined or use_network == "Testnet" %}
  {% set ws_url = "wss://s.altnet.rippletest.net:51233" %}
  {% set use_network = "Testnet" %}
{% elif use_network == "Devnet" %}
  {% set ws_url = "wss://s.devnet.rippletest.net:51233" %}
{% elif use_network == "Mainnet" %}
  {% set ws_url = "wss://xrplcluster.com" %}
{% endif %}

{{ start_step("Connect") }}
<button id="connect-button" class="btn btn-primary" data-wsurl="{{ws_url}}">{{use_network}}に接続する</button>
<div>
  <strong>接続ステータス：</strong>
  <span id="connection-status">接続されていません</span>
  <div class="loader collapse" id="loader-connect"><img class="throbber" src="assets/img/xrp-loader-96.png"></div>
</div>
{{ end_step() }}
