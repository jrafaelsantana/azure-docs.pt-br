---
title: Agendar trabalhos com o Hub IoT do Azure (.NET/.NET) | Microsoft Docs
description: Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Você pode usar o SDK do dispositivo IoT do Azure para .NET para implementar os aplicativos do dispositivo simulado e um aplicativo de serviço para executar o trabalho.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: db4824203f63fa2fe0d4256a475d18a501b17e0e
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147730"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Agendar e difundir trabalhos (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use o Hub IoT do Azure para agendar e controlar os trabalhos que atualizam milhões de dispositivos. Use trabalhos para:

* Atualizar as propriedades desejadas

* Marcas de atualização

* Chamar métodos diretos

Um trabalho encapsula uma dessas ações e controla a execução em um conjunto de dispositivos definido por uma consulta de dispositivo gêmeo. Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método direto em 10.000 dispositivos que reinicie os dispositivos. Você especifica o conjunto de dispositivos com uma consulta de dispositivo gêmeo e agenda o trabalho para execução futura. O trabalho controla o andamento conforme cada um dos dispositivos recebe e executa o método direto de reinicialização.

Para saber mais sobre cada uma dessas capacidades, consulte:

* Dispositivo gêmeo e propriedades: [Introdução ao dispositivo gêmeos](iot-hub-csharp-csharp-twin-getstarted.md) e [tutorial: Como usar as propriedades de dispositivo.](tutorial-device-twins.md)

* Métodos diretos: [Guia do desenvolvedor do Hub IOT – métodos diretos](iot-hub-devguide-direct-methods.md) e [tutorial: Usar métodos diretos](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostra como:

* Crie um aplicativo de dispositivo que implemente um método direto chamado **LockDoor**, que pode ser chamado pelo aplicativo de back-end.

* Criar um aplicativo de back-end que gere um trabalho para chamar o método direto **LockDoor** em vários dispositivos. Outro trabalho envia as atualizações de propriedade desejadas para vários dispositivos.

No fim deste tutorial, você terá dois aplicativos de console .NET (C#):

* **SimulateDeviceMethods**. Esse aplicativo se conecta ao seu hub IoT e implementa o método direto **LockDoor** .

* **ScheduleJob**. Esse aplicativo usa trabalhos para chamar o método direto **LockDoor** e atualizar as propriedades desejadas do dispositivo em vários dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Uma conta ativa do Azure. Se você não tem uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console .NET que responde a um método direto chamado pelo back-end da solução.

1. No Visual Studio, selecione **criar um novo projeto**e, em seguida, escolha o modelo de projeto **aplicativo de console (.NET Framework)** . Selecione **Avançar** para continuar.

1. Em **configurar seu novo projeto**, nomeie o projeto *SimulateDeviceMethods*e, em seguida, selecione **criar**.

    ![Configurar seu projeto do SimulateDeviceMethods](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateDeviceMethods** e selecione **gerenciar pacotes NuGet**.

1. No **Gerenciador de pacotes NuGet**, selecione **procurar** e pesquise e escolha **Microsoft. Azure. Devices. Client**. Selecione **Instalar**.

    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do dispositivo IOT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) e suas dependências.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção anterior:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Adicione o seguinte código para implementar o método direto no dispositivo:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Adicione o seguinte método para implementar o ouvinte de dispositivo gêmeos no dispositivo:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Por fim, adicione o seguinte código ao método **Principal** para abrir a conexão para o hub IoT e inicializar o ouvinte do método:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Salve seu trabalho e compile sua solução.

> [!NOTE]
> Para simplificar as coisas, este tutorial não implementa nenhuma política de repetição. No código de produção, você deve implementar políticas de repetição (como repetição de conexão), conforme sugerido em [tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Agendar trabalhos para chamar um método direto e enviar atualizações ao dispositivo gêmeo

Nesta seção, você cria um aplicativo de console .NET (usando C#) que usa trabalhos para chamar o método direto **LockDoor** e enviar as atualizações de propriedade desejadas para vários dispositivos.

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**. Em **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, nomeie o projeto *ScheduleJob*. Para **solução**, escolha **Adicionar à solução**e, em seguida, selecione **criar**.

    ![Nomeie e configure seu projeto ScheduleJob](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. Em Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ScheduleJob** e selecione **gerenciar pacotes NuGet**.

1. No **Gerenciador de pacotes NuGet**, selecione **procurar**, procure e escolha **Microsoft. Azure.** Devices e, em seguida, selecione **instalar**.

   Esta etapa baixa, instala e adiciona uma referência ao pacote NuGet do [SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) e suas dependências.

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adicione a instrução `using` a seguir caso ela não esteja presente nas instruções padrão.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua os espaços reservados pela cadeia de conexão do Hub IoT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string) e o nome do seu dispositivo.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Adicione o seguinte método à classe **Programa** :

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Adicione o seguinte método à classe **Programa** :

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Adicione outro método à classe **Programa**:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Para obter mais informações sobre a sintaxe de consulta, consulte [linguagem de consulta do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    >

1. Por fim, adicione as seguintes linhas ao método **Principal** :

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Salve seu trabalho e compile sua solução.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em sua solução e selecione **definir projetos de inicialização**.

1. Selecione **Propriedades** > comuns**projeto de inicialização**e, em seguida, selecione **vários projetos de inicialização**.

1. Certifique-se de que `SimulateDeviceMethods` está no topo da lista, seguido por `ScheduleJob`. Defina as ações a serem **iniciadas** e selecione **OK**.

1. Execute os projetos clicando em **Iniciar** ou vá para o menu **Depurar** e clique em **Iniciar depuração**.

   Você verá a saída do dispositivo e dos aplicativos back-end.

    ![Executar os aplicativos para agendar trabalhos](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

* Para continuar a introdução aos padrões do Hub IOT e do gerenciamento de dispositivos, como remoto pela atualização do firmware [do ar, leia tutorial: Como fazer uma atualização](tutorial-firmware-update.md)de firmware.

* Para saber mais sobre como implantar IA em dispositivos de borda com o Azure IoT Edge, confira [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
