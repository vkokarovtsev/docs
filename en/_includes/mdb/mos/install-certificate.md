{% list tabs group=operating_system %}

- Linux (Bash)/macOS (Zsh) {#linux-macos}

   ```bash
   mkdir -p ~/.opensearch && \
   wget "{{ crt-web-path }}" \
        --output-document ~/.opensearch/root.crt && \
   chmod 0600 ~/.opensearch/root.crt
   ```

   The certificate is saved to the `~/.opensearch/root.crt` file.

- Windows (PowerShell) {#windows}

   ```powershell
   mkdir $HOME\.opensearch; curl -o $HOME\.opensearch\root.crt {{ crt-web-path }}
   ```

   The certificate is saved to the `$HOME\.opensearch\root.crt` file.

{% endlist %}
