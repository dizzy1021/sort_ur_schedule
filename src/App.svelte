<script>
  import axios from "axios";
  import Spinner from "svelte-spinner";
  import { NotificationDisplay, notifier } from "@beyonk/svelte-notifications";

  let state = {
    nim: "",
    password: "",
    isLogin: false,
    data: [],
    clicked: false
  };

  async function actionLogin() {
    try {
      state.clicked = true;
      state.isLogin = false;

      let payload = JSON.stringify({
        nim: state.nim,
        password: state.password
      });
      const response = await axios.post(
        "https://pacific-chamber-55564.herokuapp.com/",
        payload,
        {
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
            "Accept": "application/json"
          }
        }
      );

      const data = await response;
      state.data = data.data;

      state.clicked = false;

      if (state.data !== []) {
        state.isLogin = true;
        notifier.success("Success");
      } else {
        notifier.danger("Network Error");
      }
    } catch (error) {
      state.clicked = false;
      notifier.danger("Something went Wrong");
    }
  }
</script>

<main>

  <NotificationDisplay />
  <h1>Sorting Schedule</h1>

  <div class="container">
    <div class="item">
      <input type="text" placeholder="NIM" bind:value={state.nim} />
    </div>
    <div class="item">
      <input
        type="password"
        placeholder="Password"
        bind:value={state.password} />
    </div>
    <div class="item">
      <button class="button1" disabled={state.clicked} on:click={actionLogin}>
        Login
      </button>
    </div>
  </div>
  <br />
  <div style="display: {state.clicked ? 'block' : 'none'}">
    <Spinner size="50" speed="750" color="#A82124" thickness="2" gap="40" />
  </div>

  {#if state.isLogin}
    <div id="table" style={{ marginTop: `50px`, display: 'none' }}>
      <div class="divTable scheTable">
        <div class="divTableHeading">
          <div class="divTableRow">
            <div class="divTableHead" style={{ width: `2%` }}>No</div>
            <div class="divTableHead" style={{ width: `10%` }}>Kode</div>
            <div class="divTableHead" style={{ width: `15%` }}>Mata Kuliah</div>
            <div class="divTableHead" style={{ width: `5%` }}>Kelas</div>
            <div class="divTableHead" style={{ width: `2%` }}>SKS</div>
            <div class="divTableHead">Jadwal</div>
            <div class="divTableHead" style={{ width: `5%` }}>Ruang</div>
            <div class="divTableHead" style={{ width: `25%` }}>Dosen</div>
          </div>
        </div>
        <div class="divTableBody">
          {#each state.data.data as jadwal, i}
            <div class="divTableRow">
              <div class="divTableCell">{i + 1}</div>
              <div class="divTableCell">{jadwal[2].kode}</div>
              <div class="divTableCell">{jadwal[2].matkul}</div>
              <div class="divTableCell">{jadwal[2].kelas}</div>
              <div class="divTableCell">{jadwal[2].sks}</div>
              <div class="divTableCell">
                {jadwal[2].hari + ' - ' + jadwal[2].jam}
              </div>
              <div class="divTableCell">{jadwal[2].ruang}</div>
              <div class="divTableCell">{jadwal[2].dosen}</div>
            </div>
          {/each}
        </div>
      </div>
    </div>
  {/if}
</main>
