# ST_STM32WL_SubGHz_Phy_Pocsag_Example
Example of TX and RX using Pocsag protocol on STM32WL.

It's implemented based on SubGHz_Phy_PingPong example that is included in STM32CubeWL Package (<https://www.st.com/en/embedded-software/stm32cubewl.html>)


## Preparations
* STM32CubeIDE tool (Recommanded version : 1.14.0) (<https://www.st.com/en/development-tools/stm32cubeide.html>)
* 2x NUCLEO-WL55JC1

## How to compile it?
1. Download this repository
2. Import project to STM32CubeIDE. (Project file is in "\STM32CubeIDE")
3. Build it.

## Example description
* TX - TX device will send "00 11 22 33 44 55 66 77 88 99" as payload.
* RX - RX device will receive a data that TX device sent.

## Code explanation
### radio_conf.h
    /* MANUAL_CONF_EN (1) : Preamble + Syncword + Payload */
    /* MANUAL_CONF_EN (0) : Only Payload. Preamble + Syncword will be included by radio driver */
    #define MANUAL_CONF_EN                0

### Subghz_phy_app.h
    #define USE_MODEM_FSK                 1       /* Use FSK Modulation. Only FSK was confimed for this example. */

    #define FUNC_TX                       1       /* For TX device */
    #define FUNC_RX                       0       /* For RX device */
### Subghz_phy_app.c
    #define RX_TIMEOUT_VALUE              10000    /* RX timeout is 10sec */
    #define TX_TIMEOUT_VALUE              10000    /* TX timeout is 10sec */

    /* MANUAL_CONF_EN (1) : Preamble + Syncword + Payload */
    /* MANUAL_CONF_EN (0) : Only Payload. Preamble + Syncword will be included by radio driver */
    #ifdef MANUAL_CONF_EN
    static uint8_t tx_data[20] = {
      0xaa, 0xaa, 0xaa, 0xaa, 0xaa, 0xaa,                           //Preamble 6
      0x83, 0x2d, 0xea, 0x27,                                       //Syncword 4
      0x00, 0x11, 0x22, 0x33, 0x44, 0x55, 0x66, 0x77, 0x88, 0x99    //Data 10
    };
    #else
    static uint8_t tx_data[10] = {
      0x00, 0x11, 0x22, 0x33, 0x44, 0x55, 0x66, 0x77, 0x88, 0x99    //Data 10
    };


    /* App_Process function */
    /* if TX device case, Device send packets infinitely. */
    /* if RX device case, Device show received data(payload) thoruhg UART message in OnRxDone function. */
    static void Test_Process(void)
    {
      Radio.Sleep();

      switch (State)
      {
        case TX:
        case TX_TIMEOUT:
          APP_LOG(TS_OFF, VLEVEL_L, "Tx start\n\r");
          Radio.Send(tx_data, sizeof(tx_data));
          break;

        case RX:
        case RX_TIMEOUT:
        case RX_ERROR:
          APP_LOG(TS_OFF, VLEVEL_L, "Rx start\n\r");
          Radio.Rx(RX_TIMEOUT_VALUE);
          break;
      }
    }

## Test Result
If the test result is correct, the same results as below should be produced regardless of the MANUAL_CONF_EN value.

### TX device
![TX device](https://github.com/Moris-CHO/ST_STM32WL_SubGHz_Phy_Pocsag_Example/assets/126548709/5ff40a47-cd89-4ea0-b316-9d19abd35287)
### RX device
![RX device](https://github.com/Moris-CHO/ST_STM32WL_SubGHz_Phy_Pocsag_Example/assets/126548709/71d1fe47-9c95-4e2f-a0ef-71c446015890)




