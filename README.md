# osd-xmb-plugin
plugins para o osd-xmb

### ⚠️ IMPORTANTE
```diff
! CREDITOS PELA CRIAÇÃO DO SISTEMA OSD-XMB
```
 [HiroTex](https://github.com/HiroTex/OSD-XMB)

---

📌 **Plugin para listar apps do mass e mc**

```diff! Esse plugin lista os elf dos dispositivos usb e memorycard criando uma lista no menu GAME permitindo executalos```

📌 **Plugin gme_999_xAPPS.xml**
[Uploading gme_99<App Type="SUBMENU" Category="5" Icon="FOLDER" Name="Apps">
  <Options>
    <![CDATA[
      function ReadMCDir(slot) {
          const items = [];
          const root = `mc${slot.toString()}:/APPS/`;
          const dir = System.listDir(root);
          if (!dir) { return items; }
          
          for (let i = 0; i < dir.length; i++) {
              MainMutex.unlock();
              const item = dir[i];
              if ((!item.name.toLowerCase().endsWith(".elf")) || (item.dir)) { MainMutex.lock(); continue; }
              const fileNameWithoutExtension = item.name.substring(0, item.name.length - 4);
              items.push({
                  Name: fileNameWithoutExtension,
                  Description: `${formatFileSize(item.size)}`,
                  Icon: "TOOL",
                  Type: "ELF",
                  Data: { path: root, fname: item.name }
              });
              Object.defineProperty(items[items.length - 1], "Value", {
                get() { return { Path: `${this.Data.path}${this.Data.fname}`, Args: [] }; },
                enumerable: true
              });
              MainMutex.lock();
          }
          items.sort((a, b) => {
              const nameA = a.Name.toLowerCase();
              const nameB = b.Name.toLowerCase();
              return nameA < nameB ? -1 : nameA > nameB ? 1 : 0;
          });
          return items;
      }

      function ReadUSBDir() {
          const items = [];
          const root = "mass:/APPS/";
          const dir = System.listDir(root);
          if (!dir) { return items; }
          
          for (let i = 0; i < dir.length; i++) {
              MainMutex.unlock();
              const item = dir[i];
              if ((!item.name.toLowerCase().endsWith(".elf")) || (item.dir)) { MainMutex.lock(); continue; }
              const fileNameWithoutExtension = item.name.substring(0, item.name.length - 4);
              items.push({
                  Name: fileNameWithoutExtension,
                  Description: `${formatFileSize(item.size)}`,
                  Icon: "TOOL",
                  Type: "ELF",
                  Data: { path: root, fname: item.name }
              });
              Object.defineProperty(items[items.length - 1], "Value", {
                get() { return { Path: `${this.Data.path}${this.Data.fname}`, Args: [] }; },
                enumerable: true
              });
              MainMutex.lock();
          }
          items.sort((a, b) => {
              const nameA = a.Name.toLowerCase();
              const nameB = b.Name.toLowerCase();
              return nameA < nameB ? -1 : nameA > nameB ? 1 : 0;
          });
          return items;
      }
      
      const Items = [];
      
      // USB drive logic
      if (os.readdir("mass:/")[1] > -1) {
          Items.push({
              Name: "Pendrive",
              Icon: "USBDRIVE",
              Description: "",
              Type: "SUBMENU"
          });
          Object.defineProperty(Items[Items.length - 1], "Value", {
              get() {
                  return {
                      Items: [],
                      Default: 0,
                      Init: (SubMenu) => { SubMenu.Items = ReadUSBDir(); }
                  };
              },
              enumerable: true
          });
      }
      
      // Memory Card logic
      for (let j = 0; j < 2; j++) {
          if (os.readdir(`mc${j.toString()}:/`)[1] > -1) {
              Items.push({
                  Name: `Memory Card ${(j + 1).toString()}`,
                  Icon: 16 + j,
                  Type: "SUBMENU"
              });
              Object.defineProperty(Items[Items.length - 1], "Value", {
                  get() {
                      return {
                          Items: [],
                          Default: 0,
                          Init: (SubMenu) => { SubMenu.Items = ReadMCDir(j); }
                      };
                  },
                  enumerable: true
              });
          }
      }
      return Items;
    ]]>
  </Options>
  <Init>
    <![CDATA[
      function SetDescriptions(SubMenu) {
          for (let j = 0; j < 2; j++) {
              MainMutex.unlock();
              let mcInfo = System.getMCInfo(j);
              MainMutex.lock();
              const mcItem = SubMenu.Items.find(item => item.Name === `Memory Card ${(j + 1).toString()}`);
              if (mcItem) {
                  let used = 8000 - mcInfo.freemem;
                  mcItem.Description = `${used} / 8000 Kb`;
              }
          }
      }
    ]]>
  </Init>
</App>9_xAPPS.xml…]()

```diff!O gme_999_xAPPS.xml deve ser colocado na pasta PLG do OSDXMB```

📌 **Raiz dos elf**

```diff! mass:/APPS ou mc:/APPS``` 
