<!-- // <script src="http://localhost:3000/socket.io/socket.io.js"></script> -->
<script>
  const params = window.location.search
    ?.slice(1)
    .split("&")
    .reduce((p, s) => {
      const [key, v] = s.split("=");
      p[key] = v;
      return p;
    }, {});

  const host = params["ws"] || "http://localhost:3000";
  const timeLimit = +params["s"] || 60;

  let timer = -1;
  let interval;
  let socket;

  const script = document.createElement("script");
  script.src = `${host}/socket.io/socket.io.js`;
  document.body.appendChild(script);

  setTimeout(() => {
    socket = io(host);
    socket.on("echo", function (msg) {
      restart();
    });
  }, 1000);

  function send() {
    socket.emit("echo", { timeLimit });
  }

  const restart = () => {
    clearInterval(interval);
    timer = timeLimit;
    interval = setInterval(() => {
      timer--;
      if (!timer) clearInterval(interval);
    }, 1000);
  };

  // restart();
</script>

<div class="box container">
  <div class="box" style="flex: 1; font-size: 40vw;">
    {timer < 0 ? "🐱" : timer || "👻"}
  </div>
  <div class="box button" on:click={() => send()}>
    {timer < 0 ? "Start" : "Reset"}
  </div>
</div>

<style>
  .box {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    box-sizing: border-box;
  }
  .container {
    max-width: 500px;
    width: 100%;
    height: 100%;
    background-color: blanchedalmond;
  }
  .button {
    background-color: cadetblue;
    color: white;
    font-size: 24px;
    padding: 24px;
    cursor: pointer;
  }
</style>
