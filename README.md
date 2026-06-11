# docker-images.maui

**The Problem**  

If in a GitHub action you intall the required MAUI workload it takes 3 minutes.  
If you cache it, it requires 2,5 minutes to download the cache (when the cache is hit).  

**The Solution**  

Use an image with .net 10 and MAUI workload installed.  
GitHub Action cannot use a custom image directly, so it will load this image in a Docker container.  


## Usage

Example of GitHub action usage:
```yaml
jobs:
  env:
    IMAGE: alessandropiccione/maui-dotnet10-windows:latest
  build:
    runs-on: windows-2022   # pinned

    steps:
      - name: Checkout
        uses: actions/checkout@v6

      - name: Log in to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Pull pre-baked MAUI image
        run: docker pull ${{ env.IMAGE }}

      - name: Build MAUI app
        run: |
          docker run --rm \
            -v ${{ github.workspace }}:C:\src \
            ${{ secrets.DOCKERHUB_USERNAME }}/maui-dotnet10-windows:1.0 \
            dotnet publish -f net10.0-windows10.0.19041.0 -c Release
```
