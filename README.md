# koobernooties
Very useful no survey!


## Build the docker image in minikube
Why does Docker Hub need my dockerfile? 
> [!NOTE]  
> This removes the need to publish your docker image, saving bandwidth, power and improving your privacy.

```powershell
function minik-build {
    param (
        [Parameter(Position = 0)]
        [string]$Tag = "latest",

        [Parameter(Position = 1)]
        [string]$Path = ".",
		
		[Parameter(Position = 2)]
        [switch]$NoCache
    )

    # Collect Docker environment variables before Minikube setup
    $originalEnv = @{}
    Get-ChildItem Env:DOCKER_* | foreach { $originalEnv[$_.Name] = $_.Value }

    # Set up Minikube Docker context 
    & minikube -p minikube docker-env --shell powershell | Invoke-Expression
	
    # Perform Docker build
	if($NoCache){
		docker build -t $Tag $Path --no-cache
	}else{
		docker build -t $Tag $Path
	}

    # Restore Docker environment variables to their original state
    foreach ($envVar in Get-ChildItem Env:DOCKER_*) {
        $name = $envVar.Name
        if (-not $originalEnv[$name]) {
            Remove-Item -Path "Env:$name" -Force
            continue
        }

        $originalValue = $originalEnv[$name]
        if ($envVar.Value -ne $originalValue) {
            Set-Item -Path "Env:$name" -Value $originalValue
        }
    }
}
```
