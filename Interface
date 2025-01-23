(() => {
  class BaseChat {
    activate() {}
    deactivate() {}
    addMessage() {}
    removeMessage() {}
    init() {}
  }

  class DummyChat extends BaseChat {
    constructor() {
      super();
      this._core = {
        original: null,
        dummy: null,
        messages: [],
        active: false,
        savedFlexClass: false,
        autoScroll: true
      };
    }

    init() {
      if (this._core.dummy) return;
      this._core.original = document.querySelector("#channel-chatroom");
      if (!this._core.original) return;

      const template = `
        <div
          id="dummy-chatroom"
          data-nosnippet="true"
          class="h-full max-h-full w-full min-h-0
                 group-data-[chat=false]/main:opacity-0
                 group-data-[chat=false]/main:lg:w-0
                 group-data-[chat=true]/main:lg:w-[var(--chat-width)]
                 bg-surface-lower flex grow flex-col
                 overflow-hidden lg:shrink-0 lg:grow-0
                 transition-[width,opacity] duration-300 ease-out"
          style="display:none;"
        >
          <div
            class="relative flex min-h-[38px] shrink-0 items-center justify-between
                   border-b-2 border-[#24272c] px-3.5 py-1.5 lg:min-h-[46px]"
          >
            <div class="h-fit w-fit cursor-pointer">
              <button
                id="dummy-back-button"
                class="group relative box-border flex shrink-0 grow-0 select-none items-center justify-center gap-2 whitespace-nowrap rounded font-semibold ring-0 transition-all focus-visible:outline-none active:scale-[0.95] disabled:pointer-events-none [&_svg]:size-[1em] bg-transparent focus-visible:outline-grey-300 text-white [&_svg]:fill-current betterhover:hover:bg-surface-tint lg:data-[state=open]:bg-surface-tint data-[state=active]:bg-surface-tint disabled:text-grey-600 disabled:bg-grey-1000 size-8 text-sm leading-none m-auto"
              >
                <svg
                  width="32"
                  height="32"
                  viewBox="0 0 32 32"
                  fill="white"
                  xmlns="http://www.w3.org/2000/svg"
                  style="transform: scaleX(-1)"
                >
                  <path d="M23.2095 18.3328L21.0623 20.4799L23.5324 22.9413L30 16.4737L23.5324 10.0061L21.0623 12.4674L23.4277 14.8415H2V18.3328H23.2095Z"></path>
                  <path d="M19.4905 23.4564H2.03409V26.9476H19.4905V23.4564Z"></path>
                  <path d="M19.4905 6H2.03409V9.49127H19.4905V6Z"></path>
                </svg>
              </button>
            </div>
            <span
              class="absolute left-1/2 -translate-x-1/2 text-sm font-bold lg:text-base"
            >
              Chat
            </span>
          </div>
          <div class="relative flex flex-1 min-h-0 flex-col">
            <div
              class="dummy-scroll"
              style="
                flex:1; 
                padding:8px; 
                font-family:sans-serif; 
                color:#fff; 
                overflow:auto; 
                min-height:0;
              "
            >
              <style>
                .dummy-scroll {
                  scrollbar-width: thin;
                  scrollbar-color: #666 #1E1E1E;
                }
                .dummy-scroll::-webkit-scrollbar {
                  width: 4px;
                }
                .dummy-scroll::-webkit-scrollbar-thumb {
                  background-color: #666;
                  border-radius: 4px;
                }
                .dummy-scroll::-webkit-scrollbar-track {
                  background: #1E1E1E;
                }
              </style>
              <div
                id="dummy-chat-messages"
                style="display:flex; flex-direction:column; gap:8px;"
              ></div>
            </div>
          </div>
        </div>
      `;

      const holder = document.createElement("div");
      holder.innerHTML = template.trim();
      this._core.dummy = holder.firstChild;
      this._core.original.insertAdjacentElement("beforebegin", this._core.dummy);

      this._core.dummy.querySelector("#dummy-back-button").addEventListener(
        "click",
        () => this.deactivate(true)
      );

      const scrollEl = this._core.dummy.querySelector(".dummy-scroll");
      scrollEl.addEventListener("scroll", () => {
        const distance =
          scrollEl.scrollHeight - scrollEl.scrollTop - scrollEl.clientHeight;
        this._core.autoScroll = distance <= 10;
      });
    }

    activate() {
      if (!this._core.original || !this._core.dummy || this._core.active) return;
      if (this._core.original.classList.contains("flex")) {
        this._core.original.classList.remove("flex");
        this._core.savedFlexClass = true;
      }
      this._core.original.style.display = "none";
      this._core.dummy.style.display = "flex";
      this._core.active = true;
      this._render();
    }

    deactivate(removeDummy = false) {
      if (!this._core.original || !this._core.dummy || !this._core.active) return;
      this._core.dummy.style.display = "none";
      this._core.original.style.display = "";
      if (this._core.savedFlexClass) {
        this._core.original.classList.add("flex");
        this._core.savedFlexClass = false;
      }
      this._core.active = false;
      if (removeDummy) {
        this._core.dummy.remove();
        this._core.dummy = null;
        this._core.messages = [];
      }
    }

    addMessage(a, b) {
      const msg = {};
      if (typeof a === "object") {
        msg.id = a.id || null;
        msg.chatroom_id = a.chatroom_id || null;
        msg.type = a.type || "message";
        msg.createdAt = a.created_at || new Date().toISOString();
        msg.username = a.sender?.username || "";
        msg.slug = a.sender?.slug || "";
        msg.color = a.sender?.identity?.color || "#31D6C2";
        msg.badges = a.sender?.identity?.badges || [];
        msg.content = a.content || "";
        msg.replyTo = a.reply_to || null;
        msg.mentions = a.mentions || [];
      } else {
        msg.username = a || "";
        msg.content = b || "";
        msg.color = "#31D6C2";
        msg.createdAt = new Date().toISOString();
        msg.replyTo = null;
        msg.mentions = [];
      }
      this._core.messages.push(msg);
      this._render();
    }

    removeMessage(index) {
      if (this._core.messages[index]) {
        this._core.messages.splice(index, 1);
        this._render();
      }
    }

    _render() {
      if (!this._core.dummy) return;
      const wrap = this._core.dummy.querySelector("#dummy-chat-messages");
      if (!wrap) return;
      wrap.innerHTML = "";

      this._core.messages.forEach((m) => {
        const row = document.createElement("div");
        row.style.display = "flex";
        row.style.flexDirection = "column";
        row.style.padding = "4px 6px";
        row.style.backgroundColor = "#1E1E1E";
        row.style.borderRadius = "4px";

        const header = document.createElement("div");
        header.style.display = "flex";
        header.style.alignItems = "center";

        const name = document.createElement("span");
        name.style.fontWeight = "bold";
        name.style.color = m.color;
        name.textContent = m.username;
        header.appendChild(name);

        const time = document.createElement("span");
        time.style.fontSize = "11px";
        time.style.color = "#aaa";
        time.style.marginLeft = "8px";
        time.textContent = new Date(m.createdAt).toLocaleTimeString();
        header.appendChild(time);

        row.appendChild(header);

        if (m.replyTo) {
          const replyEl = document.createElement("div");
          replyEl.style.fontSize = "12px";
          replyEl.style.color = "#ccc";
          replyEl.style.marginTop = "2px";
          replyEl.textContent = `Replying to: ${m.replyTo}`;
          row.appendChild(replyEl);
        }

        if (m.mentions && m.mentions.length > 0) {
          const mentionsEl = document.createElement("div");
          mentionsEl.style.fontSize = "12px";
          mentionsEl.style.color = "#ccc";
          mentionsEl.style.marginTop = "2px";
          mentionsEl.textContent = `Mentions: ${m.mentions.join(", ")}`;
          row.appendChild(mentionsEl);
        }

        const content = document.createElement("div");
        content.style.marginTop = "2px";
        content.style.color = "#EEE";
        content.textContent = m.content;
        row.appendChild(content);

        wrap.appendChild(row);
      });

      if (this._core.autoScroll) {
        const scrollEl = this._core.dummy.querySelector(".dummy-scroll");
        scrollEl.scrollTop = scrollEl.scrollHeight;
      }
    }
  }

  window.DummyChat = new DummyChat();
})();
