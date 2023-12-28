{% list tabs group=operating_system %}

- Linux (Bash)/macOS (Zsh) {#linux-macos}

   ```bash
   mkdir -p {{ crt-local-dir }} && \
   wget "{{ crt-web-path }}" \
        --output-document {{ crt-local-dir }}{{ crt-local-file }} && \
   chmod 0655 {{ crt-local-dir }}{{ crt-local-file }}
   ```

   The certificate will be saved to the `{{ crt-local-dir }}{{ crt-local-file }}` file.

- Windows (PowerShell) {#windows}

   ```powershell
   mkdir $HOME\.kafka; curl.exe -o $HOME\.kafka\{{ crt-local-file }} {{ crt-web-path }}
   ```

   The certificate is saved to the `$HOME\.kafka\{{ crt-local-file }}` file.

{% endlist %}
