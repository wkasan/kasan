Dim retryCount, response, holidayData, isHoliday
retryCount = 0

While retryCount < 60
    response = zm.HttpGet("https://wkasan.github.io/kasan/holiday.json")
    TracePrint "Attempt " & retryCount + 1 & " to fetch holiday data"

    If (InStr(1, response, "GfJ1rqJzgguh1QXX9pM1") > 0) then
        // Status is 0, execute operation
        isHoliday = (InStr(1, response, date()) > 0)   // Modified here: >0 means holiday

        If isHoliday Then
            TracePrint "Today is a holiday, task will NOT be executed"
            //Exit Function
        Else
            TracePrint "Today is NOT a holiday, task will be executed"
        End If

        Exit While   // Successfully fetched, exit loop
    Else
        retryCount = retryCount + 1
        TracePrint "Holiday data fetch failed, retrying..."

        If retryCount < 60 Then
            TracePrint "Retrying..."
        Else
            TracePrint "After multiple attempts, the link is still unavailable, sending notification"
            Call SendTelegram("wkasan.github.io/kasan link abnormal, need manual check!")
        End If
    End If
Wend
